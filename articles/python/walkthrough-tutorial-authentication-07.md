---
title: '연습, 7부: Azure 서비스를 사용하여 Python 앱 인증'
description: 타사 API 엔드포인트를 사용하고 Azure Queue Storage에 메시지를 쓰는 기본 앱 API 엔드포인트에 대한 코드 검사입니다.
ms.date: 08/24/2020
ms.topic: conceptual
ms.custom: devx-track-python
ms.openlocfilehash: b6a54f51c53889ba95f86ba194232262f31c2d99
ms.sourcegitcommit: 29b161c450479e5d264473482d31e8d3bf29c7c0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/06/2020
ms.locfileid: "91764701"
---
# <a name="part-7-main-application-api-endpoint"></a>7부: 기본 애플리케이션 API 엔드포인트

[이전 파트: 기본 앱 시작 코드](walkthrough-tutorial-authentication-06.md)

앱의 */api/v1/getcode* API는 영숫자 코드와 타임스탬프를 포함하는 JSON 응답을 생성합니다.

먼저 `@app.route` 데코레이터는 `get_code` 함수가 */api/v1/getcode* URL에 대한 요청을 처리한다고 Flask에 알립니다.

```python
@app.route('/api/v1/getcode', methods=['GET'])
def get_code():
```

그런 다음, URL이 `number_url`에 있는 타사 API를 호출하여 헤더의 키 자격 증명 모음에서 검색한 액세스 키를 제공합니다.

```python
    headers = {
        'Content-Type': 'application/json',
        'x-functions-key': access_key
        }

    r = requests.get(url = number_url, headers = headers)

    if (r.status_code != 200):
        return "Could not get you a code.", r.status_code
```

헤더의 `x-functions-key` 속성은 특히 Azure Functions(이 예제의 타사 API가 배포되는 위치)에서 헤더에 표시될 액세스 키를 예상하는 방식입니다. 자세한 내용은 [Azure Functions HTTP 트리거 - 권한 부여 키](/azure/azure-functions/functions-bindings-http-webhook-trigger?tabs=csharp#authorization-keys)를 참조하세요. 어떤 이유로든 API 호출이 실패하면 오류 메시지와 상태 코드를 반환합니다.

API 호출이 성공하고 숫자 값을 반환한다고 가정하면, 해당 숫자와 일부 임의 문자(자체 `random_char` 함수 사용)를 사용하여 더 복잡한 코드를 생성합니다.

```python
    data = r.json()
    chars1 = random_char(3)
    chars2 = random_char(3)
    code_value = f"{chars1}-{data['value']}-{chars2}"
    code = { "code": code_value, "timestamp" : str(datetime.utcnow()) }
```

여기에서 `code` 변수는 코드 값과 타임스탬프를 포함하는 앱 API에 대한 전체 JSON 응답을 포함합니다. 예제 응답은 `{"code":"ojE-161-pTv","timestamp":"2020-04-15 16:54:48.816549"}`입니다.

단, 이 응답을 반환하기 전에 큐 클라이언트의 [`send_message`](/python/api/azure-storage-queue/azure.storage.queue.queueclient#send-message-content----kwargs-) 메서드를 사용하여 스토리지 큐에 메시지를 작성합니다.

```python
    queue_client.send_message(code)

    return jsonify(code)
```

## <a name="processing-queue-messages"></a>큐 메시지 처리

큐에 저장된 메시지는 [Azure Portal](/azure/storage/queues/storage-quickstart-queues-portal#view-message-properties)을 통해 또는 Azure CLI 명령 [`az storage message get`](/cli/azure/storage/message#az-storage-message-get)을 사용하여 살펴보거나 관리할 수 있습니다. 샘플 리포지토리에는 앱 엔드포인트에서 코드를 요청한 다음, 메시지 큐를 확인하는 스크립트(*test.cmd* 및 *test.sh*)가 포함되어 있습니다. 또한 [`az storage message clear`](/cli/azure/storage/message#az-storage-message-clear) 명령을 사용하여 큐를 지우는 스크립트도 있습니다.

일반적으로 이 예제와 같은 앱에는 추가 처리를 위해 큐에서 메시지를 비동기식으로 끌어오는 또 다른 프로세스가 있습니다. 앞서 언급했듯이 이 API 엔드포인트에서 생성된 응답은 2단계 사용자 인증으로 앱의 다른 곳에서 사용될 수 있습니다. 그런 경우 앱은 일정 시간(예: 10분) 후에 코드를 무효화해야 합니다. 이 작업을 수행하는 간단한 방법은 사용자 로그인 절차에서 사용되는 유효한 2단계 인증 코드 테이블을 유지하는 것입니다. 그러면 앱은 다음 논리(의사 코드에서)를 사용하는 간단한 큐 감시 프로세스를 갖게 됩니다.

<pre>
pull a message from the queue and retrieve the code.

if (code is already in the table):
    remove the code from the table, thereby invalidating it
else:
    add the code to the table, making it valid
    call queue_client.send_message(code, visibility_timeout=600)
</pre>

이 의사 코드는 [`send_message`](/python/api/azure-storage-queue/azure.storage.queue.queueclient#send-message-content----kwargs-) 메서드의 선택적 `visibility_timeout` 매개 변수를 채택합니다. 이 매개 변수는 메시지가 큐에 표시되기까지의 시간(초)을 지정합니다. 기본 시간 제한은 0이므로 API 엔드포인트에서 초기에 작성된 메시지는 큐 감시 프로세스에 즉시 표시됩니다. 그 결과 해당 프로세스는 유효한 코드 테이블에 즉시 저장됩니다. 제한 시간을 통해 동일한 메시지를 다시 큐에 추가함으로써, 프로세스는 10분 후에 코드를 다시 수신한다는 것을 알고, 그 시간이 되면 테이블에서 해당 메시지가 제거됩니다.

## <a name="implementing-the-main-app-api-endpoint-in-azure-functions"></a>Azure Functions에서 기본 앱 API 엔드포인트 구현

이 문서의 앞 부분에서 설명한 코드는 Flask 웹 프레임워크를 사용하여 API 엔드포인트를 만듭니다. Flask는 웹 서버에서 실행해야 하므로 해당 코드를 Azure App Service 또는 가상 머신에 배포해야 합니다.

대체 배포 옵션은 Azure Functions의 서버리스 환경입니다. 이 경우 모든 시작 코드와 API 엔드포인트 코드는 HTTP 트리거에 바인딩된 동일한 함수 내에 포함됩니다. App Service와 마찬가지로 [함수 애플리케이션 설정](/azure/azure-functions/functions-how-to-use-azure-function-app-settings#settings)을 사용하여 코드에 대한 환경 변수를 만듭니다.

더욱 쉬워진 구현 중 하나는 Queue Storage로 인증하는 것입니다. 큐의 URL과 자격 증명 개체를 사용하여 `QueueClient` 개체를 가져오는 대신 함수에 대한 *Queue Storage 바인딩*을 만듭니다. 바인딩은 모든 인증을 내부적으로 처리합니다. 이러한 바인딩을 사용하면 함수에 즉시 사용 가능한 클라이언트 개체가 매개 변수로 제공됩니다. 자세한 내용 및 예제 코드는 [Azure Queue Storage에 Azure Functions 연결](/azure/azure-functions/functions-add-output-binding-storage-queue-cli?tabs=bash%2Cbrowser&pivots=programming-language-python)을 참조하세요.

## <a name="next-steps"></a>다음 단계

이 예제에서는 앱이 다른 Azure 서비스를 사용하여 인증하는 방법과 앱이 Azure Key Vault를 사용하여 타사 API에 필요한 다른 비밀을 저장하는 방법을 알아보았습니다.

Azure Key Vault 및 Azure Storage를 사용하여 여기에서 설명한 동일한 패턴이 다른 모든 Azure 서비스에도 적용됩니다. 중요한 단계는 Azure Portal 해당 서비스의 페이지 내에서 또는 Azure CLI를 통해 앱에 대한 올바른 역할 권한을 설정하는 것입니다. ([역할 권한을 할당하는 방법](/azure/role-based-access-control/role-assignments-steps)을 참조하세요.) 다른 액세스 정책을 구성해야 하는지 여부를 알아보려면 서비스 설명서를 참조하세요.

로컬 개발에 사용하는 서비스 주체에 동일한 역할과 액세스 정책을 할당해야 한다는 것을 항상 기억해야 합니다.

간단히 말해서 이 연습을 완료하면 해당 지식을 다른 Azure 서비스와 기타 외부 서비스에 원하는 대로 적용할 수 있습니다.

여기에서 아직 설명하지 않은 한 가지 주제는 *사용자* 인증입니다. 웹앱의 이 영역을 살펴보려면 [Azure App Service에서 엔드투엔드 사용자 인증 및 권한 부여](/azure/app-service/tutorial-auth-aad?pivots=platform-linux)부터 시작하세요.

## <a name="see-also"></a>참고 항목

- [Azure에서 Python 앱을 인증하고 권한을 부여하는 방법](azure-sdk-authenticate.md)
- 연습 샘플: [github.com/Azure-Samples/python-integrated-authentication](https://github.com/Azure-Samples/python-integrated-authentication)
- [Azure Active Directory 설명서](/azure/active-directory)
- [Azure Key Vault 설명서](/azure/key-vault)
