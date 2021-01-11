---
title: JavaScript 개발자를 위한 상위 Azure 작업
description: 현재 작업의 예를 찾습니다.
ms.topic: reference
ms.date: 01/05/2021
ms.custom: devx-track-js
ms.openlocfilehash: 2f19a660a60e91601dc31b829bb8fbc82f04b37d
ms.sourcegitcommit: 075f39972e390e79ed09a3fcfdbfc776727e08fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/06/2021
ms.locfileid: "97952485"
---
# <a name="common-top-tasks-for-javascript-developers"></a>JavaScript 개발자를 위한 일반 상위 작업

현재 작업의 예를 찾습니다. 작업을 찾을 수 없는 경우 작업을 요청하는 피드백을 남기세요. 

## <a name="app-registration"></a>앱 등록

|작업|사용|
|--|--|
|앱 등록 만들기|[포털](../tutorial/single-page-application-azure-login-button-sdk-msal.md#3-create-app-registration-for-authentication)|

## <a name="application-settings"></a>애플리케이션 설정

|작업|사용|
|--|--|
|로컬 환경 변수 설정|[Bash](../tutorial/static-web-app/create-computer-vision-resource-use-in-code.md#add-environment-variables-to-your-local-environment)|
|스토리지 컨테이너 SAS(공유 액세스 서명) 토큰 만들기|[포털](../tutorial/browser-file-upload-azure-storage-blob.md#5-generate-your-shared-access-signature-sas-token)|
|코드에서 SAS 토큰 설정|[TypeScript](../tutorial/browser-file-upload-azure-storage-blob.md#set-sas-token-in-code-file)|
|스토리지용 CORS 구성|[포털](../tutorial/browser-file-upload-azure-storage-blob.md#6-configure-cors-for-azure-storage-resource)|

## <a name="authentication"></a>인증

|작업|사용|
|--|--|
|`@azure/msal-browser`를 사용하는 Microsoft 로그인/로그오프 단추|[React/TypeScript](../tutorial/single-page-application-azure-login-button-sdk-msal.md#5-add-login-and-logoff-buttons)|
|AAD 권한 철회|[https://myapplications.microsoft.com/](https://myapplications.microsoft.com/)|
|소비자 권한 철회|[https://account.live.com/consent/manage](https://account.live.com/consent/manage)|

## <a name="azure-login"></a>Azure 로그인

|작업|사용|
|--|--|
|로그인|[Azure CLI](../tutorial/deploy-deno-app-azure-app-service-azure-cli.md#2-sign-in-to-azure-cli)<br>[Visual Studio Code 확장](../tutorial/tutorial-vscode-docker-node/tutorial-vscode-docker-node-01.md#sign-in-to-azure)|


## <a name="azure-resource-groups"></a>Azure 리소스 그룹

|작업|사용|
|--|--|
|리소스 그룹 만들기|[Azure CLI](../tutorial/static-web-app/create-computer-vision-resource-use-in-code.md#create-azure-resources)|
|리소스 그룹 삭제|[Azure CLI](../tutorial/static-web-app/clean-up-resources.md#remove-all-the-resources-by-removing-resource-group)|

## <a name="apps"></a>앱

### <a name="static-web-apps"></a>정적 웹앱

|작업|사용|
|--|--|
|Angular 앱 만들기|[Bash](../tutorial/tutorial-vscode-static-website-node/tutorial-vscode-static-website-node-02.md?tabs=angular)|
|Deno 앱 만들기|[Bash](../tutorial/deploy-deno-app-azure-app-service-azure-cli.md#3-create-local-deno-api-app)|
|JavaScript 언어를 대상으로 하는 React 앱 만들기|[Bash](../tutorial/tutorial-vscode-static-website-node/tutorial-vscode-static-website-node-02.md?tabs=react)|
|TypeScript 언어를 대상으로 하는 React 앱 만들기|[Bash](../tutorial/single-page-application-azure-login-button-sdk-msal.md#4-create-react-single-page-application-for-typescript)|
|Vue 앱 만들기|[Bash](../tutorial/tutorial-vscode-static-website-node/tutorial-vscode-static-website-node-02.md?tabs=vue)|
|Svelte 앱 만들기|[Bash](../tutorial/tutorial-vscode-static-website-node/tutorial-vscode-static-website-node-02.md?tabs=svelte)|
|정적 웹앱 만들기|[Visual Studio Code 확장](../tutorial/static-web-app/create-static-web-app-visual-studio-code-extension.md#create-a-static-web-app-resource)|
|스토리지 호스팅 정적 앱 만들기|[Visual Studio Code 확장](../tutorial/tutorial-vscode-static-website-node/tutorial-vscode-static-website-node-03.md)|
|스토리지 호스팅 정적 앱 배포|[Visual Studio Code 확장](../tutorial/tutorial-vscode-static-website-node/tutorial-vscode-static-website-node-04.md)|
|사이트 찾아보기|[Visual Studio Code 확장](../tutorial/static-web-app/create-static-web-app-visual-studio-code-extension.md#view-azure-static-web-site-in-browser)|

### <a name="function-serverless-apps"></a>함수(서버리스) 앱

|작업|사용|
|--|--|
|로컬로 함수 앱 만들기|[Visual Studio Code 확장](../tutorial/tutorial-vscode-serverless-node-create-local.md)|
|HTTP 트리거 코드|[JavaScript](../tutorial/tutorial-vscode-serverless-node-create-local.md#http-function-javascript-template-code)|
|로컬로 함수 디버그/테스트|[Visual Studio Code](../tutorial/tutorial-vscode-serverless-node-test-local.md)|
|Azure 클라우드에 함수 배포|[Visual Studio Code 확장](../tutorial/tutorial-vscode-serverless-node-deploy-hosting.md)|
|공용 URL에서 함수를 사용할 수 있는지 확인|[Visual Studio Code 확장/브라우저](../tutorial/tutorial-vscode-serverless-node-deploy-hosting.md#verify-functions-app-is-publicly-available-with-browser)|
|함수 앱 리소스 제거|[Visual Studio Code 확장](../tutorial/tutorial-vscode-serverless-node-remove-resource.md)|

### <a name="app-service---full-stack-server-only-or-client-only-apps"></a>앱 서비스 - 전체 스택, 서버 전용 또는 클라이언트 전용 앱

|작업|사용|
|--|--|
|로컬 Express.js 앱 만들기|[Bash](../tutorial/deploy-nodejs-azure-app-service-with-visual-studio-code.md?tabs=bash#3-create-a-local-expressjs-app)|
|앱 리소스 만들기 - 포함: Express.js 앱 배포, 스트림 로그|[Visual Studio Code 확장](../tutorial/deploy-nodejs-mongodb-app-service-from-visual-studio-code.md#create-web-app-resource-and-deploy-expressjs-app)|
|앱 리소스 만들기 - 포함: Express.js 앱 배포, 앱 설정 구성, npm install 실행, 배포된 웹 사이트로 이동|[Visual Studio Code 확장](../tutorial/deploy-nodejs-azure-app-service-with-visual-studio-code.md?tabs=bash#6-create-app-service-resource-in-visual-studio-code)|
|웹앱 리소스 계획 만들기|[Azure CLI](../tutorial/tutorial-vscode-azure-cli-node/tutorial-vscode-azure-cli-node-03.md#create-app-service-plan)|
|앱 리소스 만들기|[Azure CLI](../tutorial/tutorial-vscode-azure-cli-node/tutorial-vscode-azure-cli-node-03.md)|
|배포 구성|[Azure CLI](../tutorial/deploy-deno-app-azure-app-service-azure-cli.md#5-configure-the-azure-app-service-webapp)
|앱 배포|[Azure CLI](../tutorial/tutorial-vscode-azure-cli-node/tutorial-vscode-azure-cli-node-04.md)|
|브라우저에서 앱 보기|[Azure CLI](../tutorial/tutorial-vscode-azure-cli-node/tutorial-vscode-azure-cli-node-03.md#browse-web-app)|
|앱 리소스 삭제|[Visual Studio Code 확장](../tutorial/deploy-nodejs-mongodb-app-service-from-visual-studio-code.md#clean-up-resources)<br>[Azure CLI](../tutorial/tutorial-vscode-azure-cli-node/tutorial-vscode-azure-cli-node-07.md)|
|원격 로그 스트림|[Visual Studio Code 확장](../tutorial/deploy-nodejs-azure-app-service-with-visual-studio-code.md?tabs=bash#7-stream-remote-service-logs-in-visual-studio-code)<br>[Azure CLI](../tutorial/tutorial-vscode-azure-cli-node/tutorial-vscode-azure-cli-node-05.md)|

## <a name="cognitive-services"></a>Cognitive Services

|작업|사용|
|--|--|
|Cognitive Services _ComputerVision_ 리소스 만들기|[Azure CLI](../tutorial/static-web-app/create-computer-vision-resource-use-in-code.md#create-azure-resources)|
|Cognitive Services _ComputerVision_ 리소스 가져오기|[Azure CLI](../tutorial/static-web-app/create-computer-vision-resource-use-in-code.md#create-azure-resources)|
|Azure SDK 설치|[Bash](../tutorial/static-web-app/add-computer-vision-react-app.md#add-computer-vision-to-local-react-app)|
|[`@azure/cognitiveservices-computervision`](https://www.npmjs.com/package/@azure/cognitiveservices-computervision)으로 이미지 분석|[Visual Studio Code](../tutorial/static-web-app/add-computer-vision-react-app.md#add-computer-vision-code-as-separate-module)|

## <a name="containers"></a>컨테이너

원하는 항목을 찾지 못한 경우 [Docker 작업](#docker)을 확인합니다. 

|작업|사용|
|--|--|
|컨테이너 레지스트리 리소스 만들기|[Visual Studio Code 확장](../tutorial/tutorial-vscode-docker-node/tutorial-vscode-docker-node-02.md#create-an-azure-container-registry)|
|레지스트리 리소스에 이미지 푸시|[Visual Studio Code 확장](../tutorial/tutorial-vscode-docker-node/tutorial-vscode-docker-node-04.md#push-the-image-to-a-registry)|
|레지스트리에 대한 관리자 액세스 활성화|[Visual Studio Code 확장](../tutorial/tutorial-vscode-docker-node/tutorial-vscode-docker-node-05.md#enable-admin-access-on-the-registry)|
|앱 서비스에 이미지 배포|[Visual Studio Code 확장](../tutorial/tutorial-vscode-docker-node/tutorial-vscode-docker-node-05.md?branch=main#deploy-image)|


## <a name="databases"></a>데이터베이스

|작업|사용|
|--|--|
|CosmosDB 만들기 - MongoDB 리소스|[Visual Studio Code 확장](../tutorial/deploy-nodejs-mongodb-app-service-from-visual-studio-code.md)|
|CosmosDB 연결 문자열 가져오기|[Visual Studio Code 확장](../tutorial/deploy-nodejs-mongodb-app-service-from-visual-studio-code.md#get-cosmosdb-connection-string)|

## <a name="docker"></a>Docker

원하는 항목을 찾지 못한 경우 [컨테이너 작업](#containers)을 확인합니다. 

|작업|사용|
|--|--|
|Docker 버전 확인|[Bash](../tutorial/tutorial-vscode-docker-node/tutorial-vscode-docker-node-01.md#verify-docker-install)|
|로컬 프로젝트에 docker 파일 추가|[Visual Studio Code 확장](../tutorial/tutorial-vscode-docker-node/tutorial-vscode-docker-node-04.md#add-docker-files)|
|로컬 프로젝트에서 docker 이미지 빌드|[Bash](../tutorial/tutorial-vscode-docker-node/tutorial-vscode-docker-node-04.md#build-a-docker-image)|

## <a name="git"></a>Git

|작업|사용|
|--|--|
|로컬 Git 리포지토리 초기화|[Visual Studio Code 확장](../tutorial/deploy-nodejs-azure-app-service-with-visual-studio-code.md?tabs=bash#5-initialize-git-in-visual-studio-code-for-current-app)|

## <a name="github"></a>GitHub 

### <a name="actions"></a>동작 

|작업|사용|
|--|--|
|비밀 추가|[Visual Studio Code](../tutorial/static-web-app/create-static-web-app-visual-studio-code-extension.md#create-a-static-web-app-resource)|
|빌드 프로세스 보기|[GitHub 웹 사이트](../tutorial/static-web-app/create-static-web-app-visual-studio-code-extension.md#view-the-github-action-build-process)|


## <a name="monitoring"></a>모니터링

|작업|사용|
|--|--|
|리소스 만들기|[Azure CLI](../tutorial/nodejs-virtual-machine-vm/create-azure-monitoring-application-insights-web-resource.md#create-azure-monitor-resource-with-azure-cli)|



## <a name="storage"></a>스토리지

|작업|사용|
|--|--|
|리소스 만들기|[Visual Studio Code 확장](../tutorial/browser-file-upload-azure-storage-blob.md#3-create-storage-resource-with-visual-studio-extension)|
|리소스 삭제|[Visual Studio Code 확장](../tutorial/browser-file-upload-azure-storage-blob.md#clean-up-resources)|
|스토리지 호스팅 정적 앱 만들기|[Visual Studio Code 확장](../tutorial/tutorial-vscode-static-website-node/tutorial-vscode-static-website-node-03.md)|
|스토리지 호스팅 정적 앱 배포|[Visual Studio Code 확장](../tutorial/tutorial-vscode-static-website-node/tutorial-vscode-static-website-node-04.md)|

### <a name="blobs"></a>Blob

|작업|사용|
|--|--|
|[`@azure/storage-blob`](https://www.npmjs.com/package/@azure/storage-blob)으로 스토리지에서 컨테이너 만들기|[React/TypeScript](../tutorial/browser-file-upload-azure-storage-blob.md#create-storage-client-and-manage-steps)|
|[`@azure/storage-blob`](https://www.npmjs.com/package/@azure/storage-blob)으로 스토리지에 파일 업로드|[React/TypeScript](../tutorial/browser-file-upload-azure-storage-blob.md#upload-button-functionality)|
|[`@azure/storage-blob`](https://www.npmjs.com/package/@azure/storage-blob)으로 스토리지 컨테이너에서 파일 나열|[React/TypeScript](../tutorial/browser-file-upload-azure-storage-blob.md#get-list-of-blobs)|

## <a name="virtual-machines"></a>가상 머신

|작업|사용|
|--|--|
|SSH를 통해 VM에 연결|[Bash](../tutorial/nodejs-virtual-machine-vm/connect-linux-virtual-machine-ssh.md#connect-with-ssh-and-change-web-app)|
|모니터링 SDK 설치|[Bash](../tutorial/nodejs-virtual-machine-vm/connect-linux-virtual-machine-ssh.md#install-monitoring-sdk)|
|Express.js 앱에 모니터링 코드 추가|[JavaScript](../tutorial/nodejs-virtual-machine-vm/azure-monitor-application-insights-nodejs-expressjs-code.md#edit-indexjs-for-logging-with-azure-monitor-application-insights)|
|cloud-init 파일 만들기|[YAML](../tutorial/nodejs-virtual-machine-vm/create-linux-virtual-machine-azure-cli.md#create-a-cloud-init-file-to-expedite-linux-virtual-machine-creation)|
|Linux VM 리소스 만들기|[Azure CLI](../tutorial/nodejs-virtual-machine-vm/create-linux-virtual-machine-azure-cli.md#create-a-virtual-machine-resource)|
|Linux VM의 포트 열기|[Azure CLI](../tutorial/nodejs-virtual-machine-vm/create-linux-virtual-machine-azure-cli.md#open-port-for-virtual-machine)|
|로그 보기|[Azure CLI](../tutorial/nodejs-virtual-machine-vm/azure-monitor-application-insights-nodejs-expressjs-code.md#viewing-the-vm-logs-for-nginx-and-pm2)<br>[포털](../tutorial/nodejs-virtual-machine-vm/azure-monitor-application-insights-logs.md#view-application-traces-in-azure-portal)|


## <a name="visual-studio-code"></a>Visual Studio Code

|작업|사용|
|--|--|
|GitHub 리포지토리 복제|[Visual Studio Code](../tutorial/browser-file-upload-azure-storage-blob.md#2-clone-and-run-the-initial-react-app)|

## <a name="samples-supporting-these-tasks"></a>이러한 작업을 지원하는 샘플

|Name | 설명|
|--|--|
|Static 웹앱을 빌드하여 Azure에 배포|GitHub 작업을 사용하여 로컬로 React/TypeScript 클라이언트 애플리케이션을 빌드하고 Azure Static Web App에 배포합니다.<br>[자습서](../tutorial/static-web-app/introduction.md) - [샘플 코드](https://github.com/Azure-Samples/js-e2e-client-cognitive-services)|
|Azure Storage Blob에 파일 업로드|이 샘플 프로젝트는 Azure Storage Blob에 업로드할 파일을 선택하는 HTML 양식이 있는 TypeScript React(create-react-app) 프레임워크 클라이언트 앱입니다.<br>[자습서](../tutorial/browser-file-upload-azure-storage-blob.md) - [샘플 코드](https://github.com/Azure-Samples/js-e2e-browser-file-upload-storage-blob)|
|클라이언트 애플리케이션에 로그인 단추 추가|이 자습서에서 빌드하는 SPA는 다음과 같은 작업을 수행하는 React 앱(create-react-app)입니다.<br>* Office 365 또는 Outlook.com 같은 Microsoft 지원 로그인을 사용하여 로그인<br>* 애플리케이션에서 로그오프<br>[자습서](../tutorial/single-page-application-azure-login-button-sdk-msal.md) - [샘플 코드](https://github.com/Azure-Samples/js-e2e-client-azure-login-button)|
|앱 서비스에 배포된 CosmosDB(MongoDB)/Express.js 앱|이 자습서에서는 확장을 사용하여 VSCode를 사용하여 로컬에서 프로젝트를 로드하고 실행하는 방법과 앱 서비스에서 코드를 원격으로 실행하는 방법을 보여줍니다. 이 자습서에는 Mongo API용 CosmosDB 리소스 만들기, 연결 정보 가져오기, 앱 서비스 구성 설정에서 이를 설정하여 클라우드 데이터베이스에 연결 등이 있습니다.<br>[자습서](../tutorial/deploy-nodejs-mongodb-app-service-from-visual-studio-code.md#get-cosmosdb-connection-string) - [샘플 코드](https://github.com/Azure-Samples/js-e2e-express-mongo)|

## <a name="next-steps"></a>다음 단계

* [웹 앱 배포](deploy-web-app.md)