---
title: '6단계: VS Code를 사용하여 Azure Functions에 두 번째 Python 함수 추가'
description: 자습서 6단계, 두 번째 함수를 추가하여 Azure Functions 프로젝트 확장
ms.topic: conceptual
ms.date: 05/19/2020
ms.custom: devx-track-python, seo-python-october2019
ms.openlocfilehash: 6dbdd722d1167e703119b8709ec6b44eadc3dcec
ms.sourcegitcommit: 980efe813d1f86e7e00929a0a3e1de83514ad7eb
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/07/2020
ms.locfileid: "87982795"
---
# <a name="6-add-a-second-python-function-to-azure-functions"></a>6: Azure Functions에 두 번째 Python 함수 추가

[이전 단계: Azure에 배포](tutorial-vs-code-serverless-python-05.md)

첫 번째 배포 후에는 Python 함수를 추가하는 등 코드 변경 작업을 수행하고, 동일한 Azure Functions 앱에 다시 배포할 수 있습니다.

1. **Azure: Functions** 탐색기에서 **함수 만들기** 명령을 선택하거나 명령 팔레트에서 **Azure Functions: 함수 만들기**를 사용합니다. 함수에 대해 다음 세부 정보를 지정합니다.

    - 템플릿: HTTP 트리거
    - 이름: "DigitsOfPi"
    - 권한 부여 수준: 익명

1. Visual Studio Code에서 파일 탐색기는 *\_\_init\_\_.py*, *function.json* 및 *sample.dat*라는 파일을 다시 포함하는 함수 이름이 있는 하위 폴더입니다.

1. 다음 코드와 일치하도록 *\_\_init\_\_.py*의 콘텐츠를 바꿉니다. 이는 URL에 지정된 숫자로 PI의 값을 포함하는 문자열을 생성합니다(이 코드는 URL 매개 변수만 사용함).

    ```python
    import logging

    import azure.functions as func

    """ Adapted from the second, shorter solution at http://www.codecodex.com/wiki/Calculate_digits_of_pi#Python
    """

    def pi_digits_Python(digits):
        scale = 10000
        maxarr = int((digits / 4) * 14)
        arrinit = 2000
        carry = 0
        arr = [arrinit] * (maxarr + 1)
        output = ""

        for i in range(maxarr, 1, -14):
            total = 0
            for j in range(i, 0, -1):
                total = (total * j) + (scale * arr[j])
                arr[j] = total % ((j * 2) - 1)
                total = total / ((j * 2) - 1)

            output += "%04d" % (carry + (total / scale))
            carry = total % scale

        return output

    def main(req: func.HttpRequest) -> func.HttpResponse:
        logging.info('DigitsOfPi HTTP trigger function processed a request.')

        digits_param = req.params.get('digits')

        if digits_param is not None:
            try:
                digits = int(digits_param)
            except ValueError:
                digits = 10   # A default

            if digits > 0:
                digit_string = pi_digits_Python(digits)

                # Insert a decimal point in the return value
                return func.HttpResponse(digit_string[:1] + '.' + digit_string[1:])

        return func.HttpResponse(
             "Please pass the URL parameter ?digits= to specify a positive number of digits.",
             status_code=400
        )
    ```

1. 코드는 HTTP GET만 지원하기 때문에 `"methods"` 컬렉션이 `"get"`만 포함하도록 *function.json*을 수정합니다(즉 `"post"` 제거). 전체 파일은 다음과 같이 나타나야 합니다.

    ```json
    {
      "scriptFile": "__init__.py",
      "bindings": [
        {
          "authLevel": "anonymous",
          "type": "httpTrigger",
          "direction": "in",
          "name": "req",
          "methods": [
            "get"
          ]
        },
        {
          "type": "http",
          "direction": "out",
          "name": "$return"
        }
      ]
    }
    ```

1. F5 키를 누르거나 **디버그** > **디버깅 시작** 메뉴 명령을 선택하여 디버거를 시작합니다. 이제 **출력** 창에 프로젝트의 두 엔드포인트가 모두 표시됩니다.

    <pre>
    Http Functions:
            DigitsOfPi: [GET] http://localhost:7071/api/DigitsOfPi
            HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
    </pre>

1. 브라우저 또는 curl에서 `http://localhost:7071/api/DigitsOfPi?digits=125`를 요청하고 출력을 관찰합니다. (코드 알고리즘이 완전히 정확하지는 않지만 향상된 기능을 제공하는 것을 알 수 있습니다.) 완료되면 디버거를 중지합니다.

1. **Azure **:** Functions** 탐색기에서) 코드를 다시 배포합니다. 메시지가 표시되면 이전에 만든 함수 앱을 선택합니다.

1. 배포가 완료되면(몇 분이 소요됩니다.) **출력** 창에는 테스트를 반복할 수 있는 공용 엔드포인트가 표시됩니다.

> [!div class="nextstepaction"]
> [두 번째 함수를 추가했습니다. - 7단계 진행 >>>](tutorial-vs-code-serverless-python-07.md)

[문제가 발생했습니다.](https://www.research.net/r/PWZWZ52?tutorial=vscode-functions-python&step=06-second-function)
