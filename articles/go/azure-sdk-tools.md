---
title: Azure SDK for Go 사용 개발자를 위한 도구
description: Azure SDK for Go 및 Azure 서비스 작업을 위한 도구
ms.date: 09/05/2018
ms.topic: conceptual
ms.openlocfilehash: e52845218de7d874e045a11c004360b7a35baa03
ms.sourcegitcommit: 39f3f69e3be39e30df28421a30747f6711c37a7b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/21/2020
ms.locfileid: "90831309"
---
# <a name="tools-for-developers-using-the-azure-sdk-for-go"></a>Azure SDK for Go 사용 개발자를 위한 도구

Go 코드를 효율적으로 작성하고 Azure 서비스와 원활하게 작동하기 위해 몇 가지 권장 도구가 제공됩니다.

## <a name="azure-cli"></a>Azure CLI

Azure CLI는 구독에 Azure 리소스를 만들고 구성할 수 있는 명령줄 인터페이스를 제공합니다. CLI는 공통 및 공유 Azure 리소스 구축을 빠르게 시작할 수 있도록 도와줌으로써 더 복잡한 서비스 사용에 집중할 수 있게 해줍니다. CLI에는 쿼리 및 필터링 기능이 포함되므로, 원하는 명령줄 도구로 출력을 직접 파이프할 수 있습니다. CLI는 Docker 이미지로 또는 [Azure Cloud Shell](/azure/cloud-shell/overview)을 통해 로컬 시스템에 설치할 수 있도록 제공됩니다.

> [!div class="nextstepaction"]
> [Azure CLI 설치](/cli/azure/install-azure-cli)

## <a name="visual-studio-code"></a>Visual Studio Code

Visual Studio Code는 Go 지원을 제공하는 경량 편집기입니다. 이러한 확장에는 자동 완성, `impl` 템플릿, 리팩터링 및 디버깅과 같은 기능이 포함됩니다. 또한 Visual Studio Code는 소스 제어에 대한 편집기 내 액세스 및 Azure 서비스 작업을 위한 확장을 지원합니다.

* [Visual Studio Code 설치](https://code.visualstudio.com/Download)
* [Visual Studio Code Go 확장 얻기](https://code.visualstudio.com/docs/languages/go)
* [Visual Studio Code Azure 도구 확장 얻기](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-azureextensionpack)

## <a name="cicd-with-azure-devops-project"></a>Azure DevOps 프로젝트를 사용하는 CI/CD

Azure DevOps Project 파이프라인을 사용하여 Go 애플리케이션에 대한 지속적인 통합 시스템을 구축할 수 있습니다. git 리포지토리만 있으면, Azure에서 직접 배포 및 테스트할 수 있습니다.

> [!div class="nextstepaction"]
> [Azure DevOps 프로젝트를 사용하여 CI/CD 파이프라인을 만드는 방법 알아보기](/azure/devops-project/azure-devops-project-go)

## <a name="dependency-management-with-dep"></a>dep로 종속성 관리

Go용 Azure SDK는 종속성 관리를 위한 dep를 사용합니다. dep 명령을 사용하면 버전 충돌을 피하고 프로젝트가 올바르게 작동하는지 확인하여 Go 애플리케이션에 대한 벤더 요구 사항을 끌어올 수 있습니다.

> [!div class="nextstepaction"]
> [dep 종속성 관리자 얻기](https://github.com/golang/dep)