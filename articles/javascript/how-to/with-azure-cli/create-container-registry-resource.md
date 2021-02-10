---
title: 사용자 지정 컨테이너 레지스트리 만들기
description: 컨테이너 레지스트리는 Azure에 배포하려는 컨테이너 이미지에 적합합니다. 레지스트리를 사용하면 컨테이너 리포지토리 및 버전을 관리할 수 있습니다.
ms.topic: how-to
ms.date: 01/28/2021
ms.custom: devx-track-js, devx-track-azurecli
ms.openlocfilehash: d60ea6d907a628d9b68c4c44ae72c1adca65ba29
ms.sourcegitcommit: 3f8aa923e4626b31cc533584fe3b66940d384351
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/01/2021
ms.locfileid: "99230175"
---
# <a name="create-and-use-container-registry"></a>컨테이너 레지스트리 만들기 및 사용

컨테이너 레지스트리는 Azure에 배포하려는 컨테이너 이미지에 적합합니다.

레지스트리를 사용하면 컨테이너 리포지토리 및 버전을 관리할 수 있습니다.  

## <a name="create-a-container-registry"></a>컨테이너 레지스트리 만들기

리소스 이름으로 레지스트리를 만듭니다. 리소스 이름은 리소스에 대한 로그인 서버 이름의 일부가 됩니다. 

[az acr create](/cli/azure/acr#az_acr_create) 명령을 사용하여 레지스트리를 만듭니다. 

```azurecli
az acr create \
    --resource-group YOUR-RESOURCE-GROUP
    --name YOUR-REGISTRY-NAME 
    --location westus 
    --admin-enabled
    --sku Basic
    --public-network-enabled false
```

## <a name="get-container-registry-credentials"></a>컨테이너 레지스트리 자격 증명 가져오기

자격 증명을 검색하려면 [az acr credential show](/cli/azure/acr/credential#az_acr_credential_show) 명령을 실행하고 표시된 사용자 이름과 암호를 기록해 둡니다.

```azurecli
az acr credential show \
    --resource-group YOUR-RESOURCE-GROUP \
    --name YOUR-REGISTRY-NAME
```

## <a name="login-to-container-registry-with-docker-cli"></a>Docker CLI를 사용하여 컨테이너 레지스트리에 로그인

이전 단계의 자격 증명과 개별 로그인 서버를 사용하면 표준 Docker CLI 워크플로를 통해 레지스트리에 로그인할 수 있습니다.

```bash
docker login YOUR-LOGIN_SERVER \
    --username USERNAME
    --password PASSWORD
```

## <a name="tag-your-local-image"></a>로컬 이미지 태그

이제 `YOURALIAS/IMAGENAME`를 컨테이너 이미지에 지정한 이름으로 바꾸는 다음 명령을 사용하여 프라이빗 레지스트리와 연결되어 있음을 나타내도록 Docker 컨테이너에 태그를 지정할 수 있습니다.

```bash
docker tag YOURALIAS/IMAGENAME \
    YOUR-LOGIN_SERVER/YOURALIAS/IMAGENAME:v1
```

## <a name="push-your-local-image-to-your-container-registry"></a>컨테이너 레지스트리에 로컬 이미지 푸시

```bash
docker push YOUR-LOGIN_SERVER/YOURALIAS/IMAGENAME:v1
```

## <a name="configure-web-app-to-use-container"></a>컨테이너를 사용하도록 웹앱 구성 

레지스트리에서 이미지를 가져오도록 App Service 웹앱 구성에서 다음 [az appservice web config container set](/cli/azure/webapp/config/container#az_webapp_config_container_set) 명령을 실행합니다.

```azurecli
az appservice web config container set \
    --resource-group YOUR-RESOURCE-GROUP \
    --name YOUR-WEBAPP-NAME
    --docker-registry-server-url YOUR-LOGIN_SERVER \
    --docker-custom-image-name YOUR-LOGIN_SERVER/YOURALIAS/IMAGENAME:v1 \
    -u USERNAME \
    -p PASSWORD
```

`--docker-registry-server-url` 옵션의 시작 부분에 `https://` 접두사를 추가해야 합니다. 그러나 이 접두사를 컨테이너 이미지 이름에는 추가하지 마세요.

## <a name="next-steps"></a>다음 단계

* [mongodb CosmosDB 리소스 만들기](create-mongodb-cosmosdb.md)