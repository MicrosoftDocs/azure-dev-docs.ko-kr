---
title: JavaScript 개발자를 위한 상위 Azure 작업
description: 현재 작업의 예를 찾습니다.
ms.topic: reference
ms.date: 12/08/2020
ms.custom: devx-track-js
ms.openlocfilehash: 396544121a964cf7fd8cbde01012e7466a76eb37
ms.sourcegitcommit: 1c508f5ba73a12e4baeacc88ad9a8359301acb50
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/18/2020
ms.locfileid: "97690796"
---
# <a name="common-top-tasks-for-javascript-developers"></a>JavaScript 개발자를 위한 일반 상위 작업

현재 작업의 예를 찾습니다.

## <a name="application-settings"></a>애플리케이션 설정

|작업|사용|
|--|--|
|로컬 환경 변수 설정|[Bash](../tutorial/static-web-app/create-computer-vision-resource-use-in-code.md#add-environment-variables-to-your-local-environment)|
|스토리지 컨테이너 SAS(공유 액세스 서명) 토큰 만들기|[포털](../tutorial/browser-file-upload-azure-storage-blob.md#5-generate-your-shared-access-signature-sas-token)|
|코드에서 SAS 토큰 설정|[TypeScript](../tutorial/browser-file-upload-azure-storage-blob.md#set-sas-token-in-code-file)|
|스토리지용 CORS 구성|[포털](../tutorial/browser-file-upload-azure-storage-blob.md#6-configure-cors-for-azure-storage-resource)|

## <a name="azure-resource-groups"></a>Azure 리소스 그룹

|작업|사용|
|--|--|
|리소스 그룹 만들기|[Azure CLI](../tutorial/static-web-app/create-computer-vision-resource-use-in-code.md#create-azure-resources)|
|리소스 그룹 삭제|[Azure CLI](../tutorial/static-web-app/clean-up-resources.md#remove-all-the-resources-by-removing-resource-group)|

## <a name="static-web-apps"></a>정적 웹앱

|작업|사용|
|--|--|
|정적 웹앱 만들기|[Visual Studio Code 확장](../tutorial/static-web-app/create-static-web-app-visual-studio-code-extension.md#create-a-static-web-app-resource)|
|사이트 찾아보기|[Visual Studio Code 확장](../tutorial/static-web-app/create-static-web-app-visual-studio-code-extension.md#view-azure-static-web-site-in-browser)|

## <a name="storage"></a>스토리지

|작업|사용|
|--|--|
|리소스 만들기|[Visual Studio Code 확장](../tutorial/browser-file-upload-azure-storage-blob.md#3-create-storage-resource-with-visual-studio-extension)|
|리소스 삭제|[Visual Studio Code 확장](../tutorial/browser-file-upload-azure-storage-blob.md#clean-up-resources)|

### <a name="blobs"></a>Blob

|작업|사용|
|--|--|
|[`@azure/storage-blob`](https://www.npmjs.com/package/@azure/storage-blob)으로 스토리지에서 컨테이너 만들기|[React/TypeScript](../tutorial/browser-file-upload-azure-storage-blob.md#create-storage-client-and-manage-steps)|
|[`@azure/storage-blob`](https://www.npmjs.com/package/@azure/storage-blob)으로 스토리지에 파일 업로드|[React/TypeScript](../tutorial/browser-file-upload-azure-storage-blob.md#upload-button-functionality)|
|[`@azure/storage-blob`](https://www.npmjs.com/package/@azure/storage-blob)으로 스토리지 컨테이너에서 파일 나열|[React/TypeScript](../tutorial/browser-file-upload-azure-storage-blob.md#get-list-of-blobs)|

## <a name="cognitive-services"></a>Cognitive Services

|작업|사용|
|--|--|
|Cognitive Services _ComputerVision_ 리소스 만들기|[Azure CLI](../tutorial/static-web-app/create-computer-vision-resource-use-in-code.md#create-azure-resources)|
|Cognitive Services _ComputerVision_ 리소스 가져오기|[Azure CLI](../tutorial/static-web-app/create-computer-vision-resource-use-in-code.md#create-azure-resources)|
|Azure SDK 설치|[Bash](../tutorial/static-web-app/add-computer-vision-react-app.md#add-computer-vision-to-local-react-app)|
|[`@azure/cognitiveservices-computervision`](https://www.npmjs.com/package/@azure/cognitiveservices-computervision)으로 이미지 분석|[Visual Studio Code](../tutorial/static-web-app/add-computer-vision-react-app.md#add-computer-vision-code-as-separate-module)|

## <a name="github-actions"></a>GitHub 작업 

|작업|사용|
|--|--|
|비밀 추가|[Visual Studio Code](../tutorial/static-web-app/create-static-web-app-visual-studio-code-extension.md#create-a-static-web-app-resource)|
|빌드 프로세스 보기|[GitHub 웹 사이트](../tutorial/static-web-app/create-static-web-app-visual-studio-code-extension.md#view-the-github-action-build-process)|

## <a name="visual-studio-code"></a>Visual Studio Code

|작업|사용|
|--|--|
|GitHub 리포지토리 복제|[Visual Studio Code](../tutorial/browser-file-upload-azure-storage-blob.md#2-clone-and-run-the-initial-react-app)|

## <a name="next-steps"></a>다음 단계

* [웹 앱 배포](deploy-web-app.md)