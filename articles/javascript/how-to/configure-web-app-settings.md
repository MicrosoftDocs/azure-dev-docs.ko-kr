---
title: 웹앱 호스트 - 구성 설정
description: 웹앱의 일반적인 구성을 설정하는 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 12/08/2020
ms.custom: devx-track-js
ms.openlocfilehash: 29c79317d5ccd2f65db6c23e28a852164c74b743
ms.sourcegitcommit: 525c4b41d85aae9c3026a070b07e00c2241ea716
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/14/2020
ms.locfileid: "97394046"
---
# <a name="hosting-web-apps-on-azure"></a>Azure에 웹앱 호스트

웹앱의 일반적인 구성을 설정하는 방법을 알아봅니다. 일반 설정이 없는 경우 피드백에서 이슈를 열고 알려주세요. 

리소스를 만들 때 **필수 설정** 이 요청됩니다. 이때 설정이 요청되지 않으면 기본값이 있는 것이며, 리소스를 만든 후 기본값을 변경할 수 있습니다. 

## <a name="what-is-a-web-app"></a>웹앱이란?

웹앱은 인터넷 URL로 연결되는 앱을 말합니다. 웹앱으로 간주할 수 있는 수많은 Azure 서비스가 있습니다. 웹앱에 일반적으로 사용되는 최상위 서비스는 다음과 같습니다.

* 다음을 포함한 앱 서비스
    * [정적 웹앱](/azure/static-web-apps/)
    * [함수](/azure/azure-functions/)
    * [웹앱](/azure/app-service/)
    * [컨테이너](/azure/app-service/configure-custom-container?pivots=container-linux)
* 컨테이너 - [Kubernetes](/azure/aks/) 및 단일 [컨테이너](/azure/container-instances/)
* Virtual Machines - [Windows](/azure/virtual-machines/windows) 및 [Linux](/azure/virtual-machines/linux)

## <a name="how-to-configure-web-app-settings"></a>웹앱 설정을 구성하는 방법

대부분의 Azure 서비스는 설정을 구성하는 다음과 같은 네 가지 방법을 제공합니다.

* [Azure Portal](https://portal.azure.com)
* 일반적으로 관리 기능으로 표시되는 서비스용 [Azure SDK](https://github.com/Azure/azure-sdk)
* [Azure CLI](/cli/azure/)
* [Azure PowerShell](/powershell/azure/)

[확장](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice)을 통해 Visual Studio Code 내에서 많은 설정을 구성할 수도 있습니다. 

## <a name="use-default-domain-name-provided-by-azure"></a>Azure에서 제공하는 기본 도메인 이름 사용

대부분의 Azure 서비스는 리소스의 URL을 제공합니다. 서비스 이름은 Azure에서 가져온 도메인의 나머지 부분으로 하위 도메인을 결정합니다. 

예를 들어 다음과 같습니다.

* Azure Functions - `https://my-function-app.azurewebsites.net`
* Azure 웹앱 - `https://my-web-app.azurewebsites.net`
* Azure Storage Blob - `https://mystorage.blob.core.windows.net/`

정적 웹앱과 같은 일부 서비스는 상대적으로 고유한 하위 도메인을 자동으로 제공하므로, 다음과 같이 즉시 프로덕션 환경에 사용할 수 있습니다.

* Azure 정적 웹앱 = `https://gentle-tree-0b08aaf12.azurestaticapps.net`

## <a name="configure-custom-domain-name"></a>사용자 지정 도메인 이름 구성 

각 서비스는 사용자 지정 도메인을 추가하는 자체 메커니즘을 제공합니다. 

* [Azure 정적 웹앱](/azure/static-web-apps/custom-domain)
* [Azure Functions](/azure/app-service/app-service-web-tutorial-custom-domain) & [Azure 웹앱](/azure/app-service/app-service-web-tutorial-custom-domain) - 함수는 웹앱을 기반으로 하므로 동일한 메커니즘을 사용합니다.
* [Azure Storage Blob](/azure/storage/blobs/storage-custom-domain-name?tabs=azure-portal)

## <a name="configure-port-forwarding"></a>포트 전달 구성

앱의 포트가 기본 포트 `8080`이 아닌 경우 [앱의 포트 번호를 매핑](/azure/app-service/configure-language-nodejs?pivots=platform-windows#get-port-number)해야 합니다. 이렇게 하면 App 서비스에서 올바른 포트에 요청을 전달할 수 있습니다. 

## <a name="configure-certificates"></a>인증서 구성

앱에서 인증서를 즉시 요구하는 경우 여러 가지 방법으로 [인증서를 제공](/azure/app-service/configure-ssl-certificate#import-an-app-service-certificate)할 수 있습니다.

* 자체 인증서 업로드
* App 서비스 내에서 인증서 관리
* Azure Key Vault에서 인증서 가져오기
* [코드](/azure/app-service/configure-ssl-certificate-in-code)에서 인증서 제공

## <a name="configure-secrets"></a>비밀 구성

비밀은 일반적으로 다음과 같은 방법으로 제공됩니다.

* Azure Key Vault - [앱 비밀](/azure/app-service/app-service-key-vault-references)을 제공하는 이 서비스에 대한 리소스를 만듭니다. 
* 앱 설정 - 더 가벼운 솔루션을 찾고 있는 경우 비밀을 앱 설정으로 제공하고, 일반적인 [환경 변수](/azure/app-service/configure-language-nodejs?pivots=platform-windows) `process.env.VARNAME`을 사용하여 이러한 앱 설정을 참조할 수 있습니다. 

## <a name="configure-logging"></a>로깅 구성

다음과 같은 로깅이 있습니다.

* 플랫폼 로깅 - 앱 외부에서 발생
* 앱 로깅 - 앱 내부에서 발생

플랫폼 로그는 다음과 같은 목적을 위해 제공됩니다.
* 환경 상태를 이해하기 위해
* 다른 가격 책정 계층 또는 다른 지역으로 확장하기 위해 

애플리케이션 로그는 제공되지 않습니다. 내부 앱 동작에 대한 자체 로깅을 추가할 수 있습니다.
* [Azure Monitor](/azure/azure-monitor/overview)는 [Application Insights](/azure/azure-monitor/app/app-insights-overview)에 대한 npm 라이브러리를 제공하며 이 라이브러리는 로깅 및 로그가 보관되는 스토리지 리소스를 제공합니다. 

## <a name="configure-database-and-storage"></a>데이터베이스 및 스토리지 구성

일반적으로 데이터베이스 또는 데이터 스토리지에 연결하는 작업은 연결 문자열로 시작합니다. 

데이터 연결 시 고려 사항
* 현재 연결 가져오기
* 새 데이터 스토리지 - 앱에 새 스토리지 메커니즘이 필요한 경우 Azure는 [여러 가지 데이터베이스](integrate-database.md) 옵션을 제공합니다. 연결을 안전하게 저장해야 합니다. 

## <a name="missing-something"></a>빠진 내용이 있나요? 

이 목록에서 빠진 내용이 있으면 피드백을 작성하여 알려주세요. 

## <a name="next-steps"></a>다음 단계

* [엔드투엔드 Node.js 앱](/azure/developer/javascript/how-to/develop-nodejs-on-azure) 개발 흐름에서 이러한 단계를 많이 살펴보세요. 