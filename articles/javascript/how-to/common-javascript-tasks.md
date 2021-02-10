---
title: JavaScript 개발자를 위한 상위 Azure 작업
description: 현재 작업의 예를 찾습니다.
ms.topic: reference
ms.date: 01/20/2021
ms.custom: devx-track-js
ms.openlocfilehash: cc5ca751b8d22612c63d26a46934eb5b4c057c69
ms.sourcegitcommit: 71847ee0a1fee3f3320503629d9a8c82319a1f6a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/03/2021
ms.locfileid: "99511001"
---
# <a name="top-tasks-for-javascript-developers"></a>JavaScript 개발자를 위한 상위 작업

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
|앱 리소스 만들기|[Azure CLI](../tutorial/tutorial-vscode-azure-cli-node/tutorial-vscode-azure-cli-node-03.md)|
|앱 만들기, 배포, 브라우저 앱, 로그 보기|[Azure CLI](../tutorial/tutorial-vscode-azure-cli-node/tutorial-vscode-azure-cli-node-03.md)|
|데이터베이스 연결 문자열을 사용하도록 웹앱 구성|[Azure CLI](./with-azure-cli/create-mongodb-cosmosdb.md#configure-your-azure-web-app-with-the-connection-string)|
|컨테이너를 사용하도록 웹앱 구성|[Azure CLI](./with-azure-cli/create-container-registry-resource.md#configure-web-app-to-use-container)|
|웹앱 사용자 지정 도메인 이름 구성|[Azure CLI](./with-azure-cli/configure-app-service-custom-domain-name.md#register-a-domain-name-with-your-azure-app)|
|앱 리소스 삭제|[Visual Studio Code 확장](../tutorial/deploy-nodejs-mongodb-app-service-from-visual-studio-code.md#clean-up-resources)<br>[Azure CLI](../tutorial/tutorial-vscode-azure-cli-node/tutorial-vscode-azure-cli-node-07.md)|
|앱 배포 또는 재배포|[Visual Studio Code 확장](deploy-web-app.md#deploy-or-redeploy-to-app-service-with-visual-studio-code)|
|웹앱 외부 IP 가져오기|[Azure CLI](./with-azure-cli/configure-app-service-custom-domain-name.md#register-a-domain-name-with-your-azure-app)|
|도메인 이름 구매 및 DNS 레코드 구성|[Azure CLI](./with-azure-cli/configure-app-service-custom-domain-name.md#purchase-a-domain-name-and-configure-dns-record)|
|원격 로그 스트림|[Visual Studio Code 확장](../tutorial/deploy-nodejs-azure-app-service-with-visual-studio-code.md?tabs=bash#7-stream-remote-service-logs-in-visual-studio-code)<br>[Azure CLI](../tutorial/tutorial-vscode-azure-cli-node/tutorial-vscode-azure-cli-node-05.md)|

## <a name="cognitive-services"></a>Cognitive Services

|작업|사용|
|--|--|
|Cognitive Services _ComputerVision_ 리소스 만들기|[Azure CLI](../tutorial/static-web-app/create-computer-vision-resource-use-in-code.md#create-azure-resources)|
|Cognitive Services _ComputerVision_ 리소스 가져오기|[Azure CLI](../tutorial/static-web-app/create-computer-vision-resource-use-in-code.md#create-azure-resources)|
|Azure SDK 설치|[Bash](../tutorial/static-web-app/add-computer-vision-react-app.md#add-computer-vision-to-local-react-app)|
|[`@azure/cognitiveservices-computervision`](https://www.npmjs.com/package/@azure/cognitiveservices-computervision)으로 이미지 분석|[Visual Studio Code](../tutorial/static-web-app/add-computer-vision-react-app.md#add-computer-vision-code-as-separate-module)|

## <a name="containers-including-docker-tasks"></a>Docker 작업을 포함하는 컨테이너

|작업|사용|
|--|--|
|로컬 프로젝트에 docker 파일 추가|[Visual Studio Code 확장](../tutorial/tutorial-vscode-docker-node/tutorial-vscode-docker-node-04.md#add-docker-files)|
|로컬 프로젝트에서 docker 이미지 빌드|[Visual Studio Code 확장](../tutorial/tutorial-vscode-docker-node/tutorial-vscode-docker-node-04.md#build-a-docker-image)|
|로컬 JavaScript 프로젝트에서 컨테이너 이미지 만들기|[Visual Studio Code](./with-visual-studio-code/containerize-local-project.md#create-a-container)|
|컨테이너 레지스트리 리소스 만들기|[Visual Studio Code 확장](../tutorial/tutorial-vscode-docker-node/tutorial-vscode-docker-node-02.md#create-an-azure-container-registry)<br>[Azure CLI](./with-azure-cli/create-container-registry-resource.md#create-a-container-registry)|
|Dockerfile 만들기|[Visual Studio Code 확장](./with-visual-studio-code/containerize-local-project.md#create-a-dockerfile-in-your-project)|
|앱 서비스에 이미지 배포|[Visual Studio Code 확장](../tutorial/tutorial-vscode-docker-node/tutorial-vscode-docker-node-05.md#deploy-image)|
|레지스트리에 대한 관리자 액세스 활성화|[Visual Studio Code 확장](../tutorial/tutorial-vscode-docker-node/tutorial-vscode-docker-node-05.md#enable-admin-access-on-the-registry)|
|Azure 컨테이너 레지스트리 자격 증명 가져오기|[Azure CLI](./with-azure-cli/create-container-registry-resource.md#get-container-registry-credentials)|
|컨테이너 레지스트리에 로그인|[BASH - Docker CLI](./with-azure-cli/create-container-registry-resource.md#login-to-container-registry-with-docker-cli)|
|Docker 레지스트리 리소스에 이미지 푸시|[Visual Studio Code 확장](./with-visual-studio-code/containerize-local-project.md#push-local-container-image-to-dockerhub)|
|Azure 컨테이너 레지스트리 리소스에 이미지 푸시|[Visual Studio Code 확장](../tutorial/tutorial-vscode-docker-node/tutorial-vscode-docker-node-04.md#push-the-image-to-a-registry)<BR>[BASH - Docker CLI](./with-azure-cli/create-container-registry-resource.md#push-your-local-image-to-your-container-registry)|
|로컬 컨테이너 실행|[Visual Studio Code 확장](with-visual-studio-code/containerize-local-project.md#build-image-from-your-project)|
|로컬 이미지 태그|[BASH - Docker CLI](./with-azure-cli/create-container-registry-resource.md#tag-your-local-image)|
|Docker 버전 확인|[Bash](../tutorial/tutorial-vscode-docker-node/tutorial-vscode-docker-node-01.md#verify-docker-install)|

## <a name="databases"></a>데이터베이스

|작업|사용|
|--|--|
|Cosmos DB 만들기 - MongoDB 리소스|[Visual Studio Code 확장](../tutorial/deploy-nodejs-mongodb-app-service-from-visual-studio-code.md)<br>[Azure CLI](./with-azure-cli/create-mongodb-cosmosdb.md#create-a-cosmos-db-resource-for-mongodb)|
|CosmosDB 연결 문자열 가져오기|[Visual Studio Code 확장](../tutorial/deploy-nodejs-mongodb-app-service-from-visual-studio-code.md#get-cosmosdb-connection-string)<br>[Azure CLI](./with-azure-cli/create-mongodb-cosmosdb.md#get-the-mongodb-connection-string-for-your-resource)|
|Cosmos DB 보기|[Cosmos DB Explorer](https://cosmos.azure.com/)|
|Cosmos DB에서 mongoDB용 Mongoose API 사용|[JavaScript](./with-database/use-mongodb-as-cosmosdb.md#use-mongoose-sdk-to-connect-to-mongodb-on-azure)

## <a name="git"></a>Git

|작업|사용|
|--|--|
|로컬 Git 리포지토리 초기화|[Visual Studio Code 확장](../tutorial/deploy-nodejs-azure-app-service-with-visual-studio-code.md?tabs=bash#5-initialize-git-in-visual-studio-code-for-current-app)|
|로컬 분기 만들기|[명령 팔레트가 있는 Visual Studio Code](./with-visual-studio-code/clone-github-repository.md#create-a-branch-for-changes-with-git-cl)<br>[상태 표시줄이 있는 Visual Studio Code](./with-visual-studio-code/clone-github-repository.md#create-a-branch-from-status-bar)|
|GitHub에서 로컬 컴퓨터로 프로젝트 복제|[Visual Studio Code](with-visual-studio-code/install-run-debug-nodejs.md#clone-sample-project-to-local-computer)|
|원격 리포지토리에 로컬 분기 푸시|[상태 표시줄이 있는 Visual Studio Code](./with-visual-studio-code/clone-github-repository.md#push-a-local-branch-to-remote-from-status-bar)<br>[Source Course 확장이 있는 Visual Studio Code](./with-visual-studio-code/clone-github-repository.md#push-a-local-branch-to-remote-from-the-source-control-extension)|

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

## <a name="terminal-usage"></a>터미널 사용량

|작업|사용|
|--|--|
|통합 터미널|[Visual Studio Code](./with-visual-studio-code/install-run-debug-nodejs.md#use-the-integrated-bash-terminal-to-install-dependencies)|

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


## <a name="visual-studio-code-develop-and-debug-javascript-apps"></a>Visual Studio Code: JavaScript 앱 개발 및 디버그 

|작업|사용|
|--|--|
|코드 완성|[Visual Studio Code](./with-visual-studio-code/install-run-debug-nodejs.md#use-visual-studio-code-autocompletion-with-mongodb)|
|로컬 Node.js 앱 디버깅|[Visual Studio Code](./with-visual-studio-code/install-run-debug-nodejs.md#debugging-the-local-nodejs-app)|
|로컬 전체 스택 디버깅|[Visual Studio Code](with-visual-studio-code/install-run-debug-nodejs.md#local-full-stack-debugging-in-visual-studio-code)|
|프로젝트 파일 및 코드 탐색|[Visual Studio Code](./with-visual-studio-code/install-run-debug-nodejs.md#navigate-the-project-files-and-code)|
|로컬 Node.js 앱 실행|[Visual Studio Code](./with-visual-studio-code/install-run-debug-nodejs.md#running-the-local-nodejs-app)|

## <a name="samples-supporting-these-tasks"></a>이러한 작업을 지원하는 샘플

|Name | 설명|
|--|--|
|Cognitive Services를 사용하는 React 앱|GitHub 작업을 사용하여 로컬로 React/TypeScript 클라이언트 애플리케이션을 빌드하고 Azure Static Web App에 배포합니다.<br>[자습서](../tutorial/static-web-app/introduction.md) - [샘플 코드](https://github.com/Azure-Samples/js-e2e-client-cognitive-services)|
|Azure Storage Blob에 파일을 업로드하는 React 앱|이 샘플 프로젝트는 Azure Storage Blob에 업로드할 파일을 선택하는 HTML 양식이 있는 TypeScript React(create-react-app) 프레임워크 클라이언트 앱입니다.<br>[자습서](../tutorial/browser-file-upload-azure-storage-blob.md) - [샘플 코드](https://github.com/Azure-Samples/js-e2e-browser-file-upload-storage-blob)|
|로그인 단추를 사용하는 React 앱|이 자습서에서 빌드하는 SPA는 다음과 같은 작업을 수행하는 React 앱(create-react-app)입니다.<br>* Office 365 또는 Outlook.com 같은 Microsoft 지원 로그인을 사용하여 로그인<br>* 애플리케이션에서 로그오프<br>[자습서](../tutorial/single-page-application-azure-login-button-sdk-msal.md) - [샘플 코드](https://github.com/Azure-Samples/js-e2e-client-azure-login-button)|
|MongoDB 데이터베이스를 사용하는 Express.js 앱|이 자습서에서는 확장을 사용하여 VSCode를 사용하여 로컬에서 프로젝트를 로드하고 실행하는 방법과 앱 서비스에서 코드를 원격으로 실행하는 방법을 보여줍니다. 이 자습서에는 Mongo API용 CosmosDB 리소스 만들기, 연결 정보 가져오기, 앱 서비스 구성 설정에서 이를 설정하여 클라우드 데이터베이스에 연결 등이 있습니다.<br>[자습서](../tutorial/deploy-nodejs-mongodb-app-service-from-visual-studio-code.md) - [샘플 코드](https://github.com/Azure-Samples/js-e2e-express-mongo)|
|cloud-init 파일을 사용하여 VM에 배포된 Express.js 앱|Express.js 앱용 Linux VM(가상 머신)을 만듭니다. 이 VM은 cloud-init 구성 파일을 통해 구성되며 NGINX 및 Express.js 앱용 GitHub 리포지토리를 포함합니다. VM이 실행되면 SSH를 통해 VM에 연결하고, 추적 로깅을 포함하도록 웹앱을 변경하고, 웹 브라우저에서 공용 Express.js 서버 앱을 볼 수 있습니다.<br>[자습서](../tutorial/nodejs-virtual-machine-vm/introduction.md) - [샘플 코드](https://github.com/Azure-Samples/js-e2e-express-mongo)|

[Azure 샘플 브라우저](/samples/browse/?languages=javascript%2cnodejs%2ctypescript)를 사용하여 특정 사용 사례를 지원하는 더 많은 샘플을 찾을 수 있습니다. 

## <a name="next-steps"></a>다음 단계

* [웹 앱 배포](deploy-web-app.md)