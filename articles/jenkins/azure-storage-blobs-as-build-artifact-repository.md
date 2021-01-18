---
title: 자습서 - 빌드 아티팩트에 Azure Storage 사용
description: Jenkins 지속적인 통합 솔루션에서 만든 빌드 아티팩트에 대한 리포지토리로 Azure Blob 서비스를 사용하는 방법을 알아봅니다.
keywords: Jenkins, Azure, DevOps, 스토리지, CI/CD, 빌드 아티팩트
ms.topic: article
ms.date: 01/12/2021
ms.custom: devx-track-jenkins, devx-track-azurecli
ms.openlocfilehash: 31c86da8f861e4295967007cb1b885325feb93dc
ms.sourcegitcommit: 75a1f26aaff48a89631805df4b4a0c006de6a271
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/12/2021
ms.locfileid: "98128160"
---
# <a name="tutorial-use-azure-storage-for-build-artifacts"></a>자습서: 빌드 아티팩트에 Azure Storage 사용

[!INCLUDE [jenkins-integration-with-azure.md](includes/jenkins-integration-with-azure.md)]

이 문서에서는 Blob Storage를 Jenkins CI(지속적인 통합) 솔루션에서 만든 빌드 아티팩트의 리포지토리로 사용하거나 빌드 프로세스에 사용할 다운로드 가능 파일의 원본으로 사용하는 방법을 보여 줍니다. 이 솔루션은 민첩한 개발 환경에서 코딩하고(Java 또는 다른 언어 사용) 빌드가 연속 통합을 기반으로 실행 중이며, 다른 조직 구성원이나 고객과 빌드 아티팩트를 공유하거나 보관 파일을 유지 관리할 수 있도록 빌드 아티팩트의 리포지토리가 필요한 경우와 같은 시나리오에서 유용합니다. 다른 시나리오는 빌드 작업 자체에 종속성 등의 다른 파일이 빌드 입력의 다운로드로 필요한 경우입니다.

## <a name="prerequisites"></a>사전 요구 사항

- **Azure 구독**: Azure 구독이 아직 없는 경우 시작하기 전에 [Azure 체험 계정을 만듭니다](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- **Jenkins 서버**: Jenkins 서버가 설치되어 있지 않다면 [Azure에서 Jenkins 서버를 만듭니다](./configure-on-linux-vm.md).
- **Azure CLI**: Jenkins 서버에 Azure CLI(버전 2.0.67 이상)를 설치합니다.
- **Azure Storage 계정**: 스토리지 계정이 없는 경우 [스토리지 계정을 만듭니다](/azure/storage/common/storage-account-create).

## <a name="add-azure-credential-needed-to-execute-azure-cli"></a>Azure CLI를 실행하는 데 필요한 Azure 자격 증명 추가

1. Jenkins 포털로 이동합니다.

1. 메뉴에서 **Jenkins 관리** 를 선택합니다.

1. **자격 증명 관리** 를 선택합니다.

1. **글로벌** 도메인을 선택합니다.

1. **자격 증명 추가** 를 선택합니다.

1. 다음과 같은 필수 필드를 입력합니다.

    - **종류**: **사용자 이름 및 암호** 를 선택합니다.
    - **사용자 이름**: 서비스 주체의 `appId`를 지정합니다.
    - **암호**: 서비스 주체의 `password`를 지정합니다.
    - **ID**: 자격 증명 식별자(예: `azuresp`)를 지정합니다.
    - **설명**: 필요에 따라 환경에 대한 의미 있는 설명을 포함합니다.

1. **확인** 을 선택하여 자격 증명을 만듭니다.

## <a name="create-a-pipeline-job-to-upload-build-artifacts"></a>빌드 아티팩트를 업로드하는 파이프라인 작업 만들기

다음 단계는 파이프라인 작업을 만드는 과정을 안내합니다. 파이프라인 작업은 여러 파일을 만들고 Azure CLI를 사용하여 스토리지 계정에 해당 파일을 업로드합니다.

1. Jenkins 대시보드에서 **새 항목** 을 선택합니다.

1. 작업에 **myjob** 이라는 이름을 지정하고 **파이프라인**, **확인** 을 차례로 선택합니다.

1. 작업 구성의 **파이프라인** 섹션에서 **파이프라인 스크립트** 를 선택하고 다음을 **스크립트** 에 붙여넣습니다. 자리 표시자는 사용자 환경의 값에 맞게 편집하세요.

    ```groovy
    pipeline {
      agent any
      environment {
        AZURE_SUBSCRIPTION_ID='99999999-9999-9999-9999-999999999999'
        AZURE_TENANT_ID='99999999-9999-9999-9999-999999999999'
        AZURE_STORAGE_ACCOUNT='myStorageAccount'
      }
      stages {
        stage('Build') {
          steps {
            sh 'rm -rf *'
            sh 'mkdir text'
            sh 'echo Hello Azure Storage from Jenkins > ./text/hello.txt'
            sh 'date > ./text/date.txt'
          }
    
          post {
            success {
              withCredentials([usernamePassword(credentialsId: 'azuresp', 
                              passwordVariable: 'AZURE_CLIENT_SECRET', 
                              usernameVariable: 'AZURE_CLIENT_ID')]) {
                sh '''
                  echo $container_name
                  # Login to Azure with ServicePrincipal
                  az login --service-principal -u $AZURE_CLIENT_ID -p $AZURE_CLIENT_SECRET -t $AZURE_TENANT_ID
                  # Set default subscription
                  az account set --subscription $AZURE_SUBSCRIPTION_ID
                  # Execute upload to Azure
                  az storage container create --account-name $AZURE_STORAGE_ACCOUNT --name $JOB_NAME --auth-mode login
                  az storage blob upload-batch --destination ${JOB_NAME} --source ./text --auth-mode login
                  # Logout from Azure
                  az logout
                '''
              }
            }
          }
        }
      }
    }
    ```
    
1. **지금 빌드** 를 선택하여 **myjob** 을 실행합니다.

1. 콘솔 출력을 점검하여 상태를 확인합니다. 빌드 후 작업이 빌드 아티팩트를 업로드하면 Azure 스토리지에 대한 상태 메시지가 콘솔에 기록됩니다.

1. 다음과 유사한 오류가 발생하는 경우 컨테이너 수준에서 액세스 권한을 부여해야 한다는 의미입니다. `ValidationError: You do not have the required permissions needed to perform this operation.` 이 오류 메시지가 표시되면 다음 문서를 참조하여 문제를 해결하세요.

    - [Azure CLI를 사용하여 Blob 데이터에 대한 액세스 권한을 부여하는 방법 선택 - Azure Storage](/azure/storage/blobs/authorize-data-operations-cli)
    - [Azure Portal을 사용하여 데이터에 액세스하기 위한 Azure 역할 할당 - Azure Storage](/azure/storage/common/storage-auth-aad-rbac-portal)

1. 작업이 성공적으로 완료되면 공용 Blob을 열어 빌드 아티팩트를 검사하세요.

    1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
    1. **스토리지** 를 선택합니다.
    1. Jenkins에 사용한 스토리지 계정 이름을 선택합니다.
    1. **컨테이너** 를 선택합니다.
    1. Blob 목록 내에서 **myjob** 이라는 컨테이너를 선택합니다.
    1. 다음 **hello.txt** 및 **date.txt** 라는 두 파일이 표시됩니다.
    1. 이 항목 중 하나의 URL을 복사하여 브라우저에 붙여넣습니다. 
    1. 빌드 아티팩트로 업로드된 텍스트 파일이 표시됩니다.
    
    **참고**:

    - 컨테이너 이름 및 Blob 이름은 Azure Storage에서 소문자(및 대/소문자 구분)입니다.

## <a name="create-a-pipeline-job-to-download-from-azure-blob-storage"></a>Azure Blob Storage에서 다운로드할 파이프라인 작업 만들기

다음 단계에서는 Azure Blob Storage에서 항목을 다운로드하는 파이프라인 작업을 구성하는 방법을 보여 줍니다.

1. 작업 구성의 **파이프라인** 섹션에서 **파이프라인 스크립트** 를 선택하고 다음을 **스크립트** 에 붙여넣습니다. 자리 표시자는 사용자 환경의 값에 맞게 편집하세요.

    ```groovy
    pipeline {
      agent any
      environment {
        AZURE_SUBSCRIPTION_ID='99999999-9999-9999-9999-999999999999'
        AZURE_TENANT_ID='99999999-9999-9999-9999-999999999999'
        AZURE_STORAGE_ACCOUNT='myStorageAccount'
      }
      stages {
        stage('Build') {
          steps {
            withCredentials([usernamePassword(credentialsId: 'azuresp', 
                            passwordVariable: 'AZURE_CLIENT_SECRET', 
                            usernameVariable: 'AZURE_CLIENT_ID')]) {
              sh '''
                # Login to Azure with ServicePrincipal
                az login --service-principal -u $AZURE_CLIENT_ID -p $AZURE_CLIENT_SECRET -t $AZURE_TENANT_ID
                # Set default subscription
                az account set --subscription $AZURE_SUBSCRIPTION_ID
                # Execute upload to Azure
                az storage blob download --account-name $AZURE_STORAGE_ACCOUNT --container-name myjob --name hello.txt --file ${WORKSPACE}/hello.txt --auth-mode login
                # Logout from Azure
                az logout
              '''   
            }
          }
        }
      }
    }
    ```
    
1. 빌드를 실행한 후 빌드 기록 콘솔의 출력을 확인합니다. 또는 다운로드 위치를 확인하여 필요한 Blob이 성공적으로 다운로드되었는지 확인할 수도 있습니다.  

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure의 Jenkins](/azure/Jenkins/)