---
title: Azure에 대한 GitHub Actions에서 변수 바꾸기 사용
description: 매개 변수가 있는 파일의 변수를 바꾸는 GitHub Action
author: juliakm
ms.author: jukullam
ms.topic: conceptual
ms.service: azure
ms.date: 01/25/2021
ms.custom: github-actions-azure
ms.openlocfilehash: e2a82fbcbe48269339dc672d46aca4cc3601ae12
ms.sourcegitcommit: 6fbf9e489b194586887a2c11152044be5b3a2b99
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/25/2021
ms.locfileid: "98759496"
---
# <a name="use-variable-substitution-with-github-actions"></a>GitHub Actions에서 변수 바꾸기 사용

[변수 바꾸기 작업](https://github.com/marketplace/actions/variable-substitution)을 사용하여 XML, JSON 및 YAML 기반 구성과 매개 변수 파일의 값을 바꾸는 방법에 대해 알아봅니다.

워크플로를 실행하는 동안 변수 바꾸기를 통해 [GitHub 비밀](https://docs.github.com/en/actions/reference/encrypted-secrets)을 비롯한 값을 리포지토리의 파일에 삽입할 수 있습니다. 예를 들어 워크플로를 실행하는 동안 API 로그인 및 암호를 JSON 파일에 삽입할 수 있습니다.

변수 바꾸기는 개체 계층 구조에 미리 정의된 키에만 작동합니다. 변수 바꾸기를 통해 새 키를 만들 수 없습니다. 또한 이미 사용 가능한 워크플로 또는 시스템 변수에서 [환경 변수](https://docs.github.com/en/actions/reference/environment-variables)로 정의된 변수만 바꾸기에 사용할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

- GitHub 계정. 없는 경우 [평가판](https://github.com/join)에 등록하세요.  

## <a name="use-the-variable-substitution-action"></a>변수 바꾸기 작업 사용

이 예제에서는 [변수 바꾸기 작업](https://github.com/marketplace/actions/variable-substitution)을 사용하여 `employee.json`의 값을 바꾸는 방법을 안내합니다.

1. 리포지토리의 루트 수준에서 `employee.json`을 만듭니다.

    ```json
    {
        "first-name": "Toni",
        "last-name": "Cranz",
        "username": "",
        "password": "",
        "url": ""
    }
    ```

2. GitHub 리포지토리를 열고 **설정** 으로 이동합니다.

    :::image type="content" source="media/github-repo-settings.png" alt-text="탐색에서 설정 선택":::

3. **비밀** 과 **새 비밀** 을 차례로 선택합니다.

    :::image type="content" source="media/select-secrets.png" alt-text="비밀 추가 선택":::

4. 새 비밀 `PASSWORD`를 `5v{W<$2B<GR2=t4#` 값(또는 선택한 암호)으로 추가합니다. 비밀을 저장합니다. 

5. **작업** 으로 이동하여 **워크플로 직접 설정** 을 선택합니다.

6. 워크플로 파일을 추가합니다. json 파일의 사용자 이름 값이 `tcranz`로 바뀝니다. 암호는 GitHub 비밀로 바뀝니다. url 필드에는 GitHub 변수 `github.repository`를 포함하는 URL이 채워집니다.

    ```yaml
    on: [push]
    name: variable substitution in json

    jobs:
    build:
        runs-on: ubuntu-latest
        steps:
        - uses: actions/checkout@v2
        - uses: microsoft/variable-substitution@v1 
        with:
            files: 'employee.json'
        env:
            username: tcranz
            password: ${{ secrets.PASSWORD }}
            url: https://github.com/${{github.repository}}

    ```

7. **작업** 으로 이동하여 워크플로 실행을 확인합니다. 변수 바꾸기 작업을 엽니다. 각 변수가 바뀐 것을 볼 수 있습니다.

    ```text
    SubstitutingValueonKeyWithString username tcranz
    SubstitutingValueonKeyWithString password ***
    SubstitutingValueonKeyWithString url https://github.com/account/variable-sub
    Successfully updated file: employee.json
    ```

## <a name="clean-up-resources"></a>리소스 정리

GitHub 리포지토리가 더 이상 필요 없으면 삭제합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [GitHub Actions를 사용하여 Azure Web Apps에 배포](/azure/app-service/deploy-github-actions)
