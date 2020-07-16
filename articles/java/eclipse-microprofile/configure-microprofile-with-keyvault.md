---
title: Azure Key Vault를 사용하여 MicroProfile 구성
description: Azure Key Vault를 사용하여 MicroProfile 웹 서비스에 암호를 주입하는 방법 알아보기
services: key-vault
documentationcenter: java
author: jonathangiles
ms.author: jogiles
ms.date: 09/07/2018
ms.service: key-vault
ms.tgt_pltfrm: multiple
ms.topic: article
ms.workload: web
ms.custom: devx-track-java
ms.openlocfilehash: d38387482e81a1418a0e4d76b217e0a1ba1d96ac
ms.sourcegitcommit: 44016b81a15b1625c464e6a7b2bfb55938df20b6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/14/2020
ms.locfileid: "86379897"
---
# <a name="configure-microprofile-with-azure-key-vault"></a>Azure Key Vault를 사용하여 MicroProfile 구성

이 자습서는 Azure Key Vault에 직접 연결을 생성하기 위해 [MicroProfile Config API](https://microprofile.io/project/eclipse/microprofile-config)를 사용하여 [Azure Key Vault ](https://azure.microsoft.com/services/key-vault/)에서 비밀을 검색하도록 [MicroProfile](http://microprofile.io) 애플리케이션을 구성하는 방법을 보여줍니다. MicroProfile Config API를 사용하여 개발자는 구성 데이터를 검색하고 마이크로 서비스에 주입하는 데 있어서의 표준 API의 이점을 누릴 수 있습니다.

들어가기 전에, Azure Key Vault와 MicroProfile Config API의 조합을 통해 우리가 작성할 수 있는 코드를 신속하게 살펴보도록 합니다. 다음은 `@Inject` 및 `@ConfigProperty`로 주석 처리 된 클래스 필드의 코드 조각입니다. 주석에 지정된 `name`은 Azure Key Vault에서 조회 할 속성의 이름이고 `defaultValue`는 키가 발견되지 않은 경우 설정되는 것입니다. 결과적으로 Azure Key Vault에 저장된 값 또는 기본값은 런타임 시 필드에 자동으로 주입되어 개발자가 더 이상 constuctors 및 setter 메서드에서 값을 전달할 필요가 없으며 MicroProfile에서 처리하게 하므로 개발자의 작업을 단순화합니다.

```java
@Inject
@ConfigProperty(name = "key-name", defaultValue = "Unknown")
String keyValue;
```

필요에 따라 비밀을 요청하려면 MicroProfile 구성에 직접 액세스할 수도 있습니다. 예:

```java
public class DemoClass {
    @Inject
    Config config;

    public void method() {
        System.out.println("Hello: " + config.getValue("key-name", String.class));
    }
}
```

이 예제에서는 [Payara Micro](https://www.payara.fish/payara_micro) 및 [MicroProfile](https://microprofile.io/)을 사용하여 컴퓨터에서 로컬로 실행할 수 있는 작은 Java war 파일을 만듭니다. Azure에 코드를 컨테이너화하거나 푸시하는 방법을 보여주지는 않지만, 이 자습서의 끝 부분에 있는 링크 섹션은 이것을 설명하는 다른 유용한 자습서에 대한 링크를 제공합니다.

이 예제에서는 Azure Key Vault에 대한 구성 소스(MicroProfile 구성 API 사용)를 생성하는 무료 및 오픈 소스 라이브러리를 사용합니다. [프로젝트 GitHub 페이지](https://github.com/Azure/azure-microprofile/tree/master/microprofile-config-keyvault)에서 이 라이브러리에 대해 자세히 알아보고 코드를 검토할 수 있습니다. 이 자습서의 코드는 이 라이브러리를 사용하여 라이브러리 구성에 초점을 맞추고, 코드에 키를 주입할 수 있으며 Azure 관련 코드를 작성할 필요가 없습니다.

Azure Key Vault 리소스를 만드는 것으로 시작하여 로컬 시스템에서 이 코드를 실행하는 데 필요한 단계는 다음과 같습니다.

## <a name="creating-an-azure-key-vault-resource"></a>Azure Key Vault 리소스 만들기

Azure Key Vault 리소스를 만들고 하나의 비밀로 채우기 위해 Azure CLI를 사용합니다.

1. 우선, Azure 서비스 주체를 만들어 봅시다. Key Vault에 액세스하기 위해 필요한 클라이언트 ID 및 키를 제공합니다.

```bash
az login
az account set --subscription <subscription_id>

az ad sp create-for-rbac --name <service_principal_name>
```

예를 들어 이전 단계에서 서비스 사용자 이름으로 `microprofile-keyvault-service-principal`을 사용합니다. 이 작업을 수행한 경우의 Azure의 응답은 다음과 같습니다. 약간 검열된 양식입니다.

```json
{
  "appId": "5292398e-XXXX-40ce-XXXX-d49fXXXX9e79",
  "displayName": "microprofile-keyvault-service-principal",
  "name": "http://microprofile-keyvault-service-principal",
  "password": "9b217777-XXXX-4954-XXXX-deafXXXX790a",
  "tenant": "72f988bf-XXXX-41af-XXXX-2d7cd011db47"
}
```

여기서 주목할 것은 `appID`, `password` 값입니다. 이것들은 나중에 각각 `client ID`와 `key`로 사용할 것입니다.

이제 서비스 주체를 만들었으므로 선택적으로 리소스 그룹을 만들 수 있습니다(이미 사용하려는 리소스 그룹이 있는 경우 이 단계를 건너뛸 수 있음). 리소스 그룹 위치 목록을 얻으려면 `az account list-locations`를 호출하고 해당 목록의 `name` 값을 사용하여 리소스 그룹을 작성해야 하는 위치를 지정할 수 있습니다.

```bash
# For this tutorial, the author chose to use `westus`
# and `jg-test` for the resource group name.
az group create -l <resource_group_location> -n <resource_group_name>
```

이제 Azure Key Vault 리소스를 만듭니다. Key Vault 이름은 나중에 Key Vault를 참조하는 데 사용되므로 기억할 만한 것을 선택하십시오.

```bash
az keyvault create --name <your_keyvault_name>            \
                   --resource-group <your_resource_group> \
                   --location <location>                  \
                   --enabled-for-deployment true          \
                   --enabled-for-disk-encryption true     \
                   --enabled-for-template-deployment true \
                   --sku standard
```

앞서 생성한 서비스 주체에 대해 적절한 사용 권한을 부여해야 Key Vault 비밀에 액세스할 수 있습니다. appID 값은 서비스 보안 주체(즉, `5292398e-XXXX-40ce-XXXX-d49fXXXX9e79` - 하지만 터미널 출력의 값을 사용)를 생성한 곳의 위에 있는 `appId` 값입니다.

```bash
az keyvault set-policy --name <your_keyvault_name>   \
                       --secret-permission get list  \
                       --spn <your_sp_appId_created_in_step1>
```

이제 Key Vault에 비밀을 푸시할 수 있습니다. `demo-key` 키 이름을 사용하고 키 값을 `demo-value`로 설정합니다.

```bash
az keyvault secret set --name demo-key      \
                       --value demo-value   \
                       --vault-name <your_keyvault_name>  
```

이것으로 끝입니다. 이제 단일 비밀을 사용하여 Azure에서 실행되는 Key Vault를 얻었습니다. 이제 이 리포지토리를 복제하고 앱에서 이 리소스를 사용하도록 구성할 수 있습니다.

## <a name="getting-up-and-running-locally"></a>시작하여 로컬로 실행

이 예제는 GitHub에서 사용할 수 있는 예제 애플리케이션을 기반으로 하므로, 코드를 복제한 다음, 코드 단계를 따릅니다. 컴퓨터에 코드를 복제하려면 다음 단계를 수행합니다.

1. `git clone https://github.com/Azure-Samples/microprofile-configsource-keyvault.git`

1. `cd microprofile-configsource-keyvault`

1. `src/main/resources/META-INF/microprofile-config.properties`로 이동하고 위에 있는 세부 사항으로 microprofile-config.properties 파일의 속성을 변경하십시오.

1. `mvn clean package payara-micro:start`를 사용하여 서버를 구동합니다.

1. 웹 브라우저에서 `http://localhost:8080/keyvault-configsource/api/config`에 액세스합니다. 그러면 Azure Key Vault에서 읽는 값을 보여 주는 간단한 응답이 표시됩니다.

## <a name="summary"></a>요약

이 애플리케이션 예제는 MicroProfile 구성 API, Azure Key Vault 및 무료 및 오픈 소스 [microprofile-config-keyvault](https://github.com/Azure/azure-microprofile/tree/master/microprofile-config-keyvault) 라이브러리를 함께 사용하여 MicroProfile 웹 서비스에 구성 데이터와 비밀 정보를 쉽게 주입할 수 있도록 합니다.
