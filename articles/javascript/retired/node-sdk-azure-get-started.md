---
title: Node.js용 Azure 모듈 시작
description: Node.js용 Azure 모듈을 사용하여 인증 및 리소스 관리 시작
ms.date: 06/17/2017
ms.topic: conceptual
ms.openlocfilehash: 600c10c052a3aa95eb4dc04de0166a6974ede060
ms.sourcegitcommit: 553da4e9aa988e5bb823364244ea81961cee5bc7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/01/2020
ms.locfileid: "85792638"
---
# <a name="get-started-with-the-azure-modules-for-nodejs"></a>Node.js용 Azure 모듈 시작

이 가이드에서는 Azure Node.js 모듈을 설치하고, 서비스 사용자로 Azure에 인증하고, Azure 구독에 리소스를 만들어 Azure 클라우드 서비스에 연결하는 샘플 코드를 실행하는 과정을 안내합니다.

## <a name="prerequisites"></a>사전 요구 사항

- Azure 계정. 계정이 없으면 [체험 계정을 얻습니다](https://azure.microsoft.com/free/).
- [Node.JS](https://nodejs.org)
- [Azure Cloud Shell](/azure/cloud-shell/quickstart) 또는 [Azure CLI 2.0](/cli/azure/install-az-cli2)

[!INCLUDE [azure-cloud-shell](../includes/cloud-shell-try-it.md)]

## <a name="prepare-your-environment"></a>환경 준비

빈 디렉터리에 새 프로젝트를 만들고 다음 npm 모듈을 설치합니다.

```bash
cd azure-node-quickstart
npm init -y
npm install --save azure ms-rest-azure azure-arm-compute azure-arm-network azure-storage azure-arm-storage
```

## <a name="set-up-authentication"></a>인증 설정

이 가이드에서 샘플 코드를 실행하려면 Azure 구독에 대한 읽기 및 만들기 권한이 Node.js 애플리케이션에 필요합니다. 서비스 사용자를 만들고 해당 자격 증명을 사용하여 실행되도록 애플리케이션을 구성합니다. 서비스 사용자는 앱에서 실행하는 데 필요한 권한만 부여하는 ID와 연결되는 비대화형 계정입니다.

[Azure CLI 2.0을 사용하여 서비스 사용자를 만들고](/cli/azure/create-an-azure-service-principal-azure-cli) 출력을 캡처합니다. 암호 인수에 `MY_SECURE_PASSWORD` 대신 [보안 암호](/azure/active-directory/active-directory-passwords-policy)를 제공해야 합니다.

```azurecli
az ad sp create-for-rbac --name AzureNodeTest --password MY_SECURE_PASSWORD
```

```json
{
  "appId": "a487e0c1-82af-47d9-9a0b-af184eb87646d",
  "displayName": "AzureNodeTest",
  "name": "http://AzureNodeTest",
  "password": password,
  "tenant": ""
}
```

*appId*, *password* 및 *tenant* 값을 환경 변수로 내보냅니다.

```bash
export AZURE_ID a487e0c1-82af-47d9-9a0b-af184eb87646d
export AZURE_PASS password
export AZURE_TENANT XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX
```

[az account show](/cli/azure/account#az-account-show)를 사용하여 구독 ID를 가져옵니다.

```azurecli
az account show
```

```json
{
   "environmentName": "AzureCloud",
   "id": "306943934-0323-4ae4d-a42b-f6613d1664ac",
   "isDefault": true
}
```

구독 ID를 환경 변수로 내보냅니다.

```bash
export AZURE_SUB 306943934-0323-4ae4d-a42b-f6613d1664ac
```

## <a name="create-a-linux-virtual-machine"></a>Linux 가상 머신 만들기

다음 코드를 사용하여 현재 디렉터리에 *createVM.js* 파일을 새로 만듭니다. `adminPass`의 값을 유효한 암호로 업데이트합니다.

```javascript
'use strict';

const MsRest = require('ms-rest-azure');
const ComputeManagementClient = require('azure-arm-compute');
const NetworkManagementClient = require('azure-arm-network');

MsRest.loginWithServicePrincipalSecret(
    process.env.AZURE_ID, process.env.AZURE_PASS, process.env.AZURE_TENANT, (err, credentials) => {

        let adminPass = YOUR_VALUE_HERE;
        const networkClient = new NetworkManagementClient(credentials, process.env.AZURE_SUB);
        const computeClient = new ComputeManagementClient(credentials, process.env.AZURE_SUB);

        let nicParameters = {
            location: "eastus",
            ipConfigurations: [
                {
                    name: "vmnetinterface",
                    privateIPAllocationMethod: 'Dynamic',
                }
            ]
        };

        const vnetParameters = {
            location: "eastus",
            addressSpace: {
                addressPrefixes: ['10.0.0.0/16']
            },
            dhcpOptions: {
                dnsServers: ['10.1.1.1', '10.1.2.4']
            },
            subnets: [{ name: "mynodesubnet", addressPrefix: '10.0.0.0/24' }],
        };

        let vmParameters = {
            location: "eastus",
            osProfile: {
                computerName: "newLinuxVM",
                adminUsername: "testadmin",
                adminPassword: admin_password
            },
            hardwareProfile: {
                vmSize: 'Basic_A1'
            },
            networkProfile: {
                networkInterfaces: [
                    {
                        primary: true
                    }
                ]
            },
            storageProfile: {
                imageReference: {
                    publisher: 'Canonical',
                    offer: 'UbuntuServer',
                    sku: '16.04-LTS',
                    version: 'latest'
                },
            }
        };

        let publicIPParameters = {
            location: "eastus",
            publicIPAllocationMethod: 'Dynamic'
        };

        networkClient.virtualNetworks.createOrUpdate("myResourceGroup", "mynodevnet", vnetParameters)
            .then(function (vnetwork) {
                networkClient.subnets.get("myResourceGroup", "mynodevnet", "mynodesubnet")
                    .then(function (subnetInfo) {
                        nicParameters.ipConfigurations[0].subnet = subnetInfo;
                        networkClient.publicIPAddresses.createOrUpdate("myResourceGroup", "myLinuxPublicIP", publicIPParameters)
                            .then(function (publicIP) {
                                nicParameters.ipConfigurations[0].publicIPAddress = publicIP;
                                networkClient.networkInterfaces.createOrUpdate("myResourceGroup", "vmnetinterface", nicParameters)
                                    .then(function (vmNetworkInterface) {
                                        vmParameters.networkProfile.networkInterfaces[0].id = vmNetworkInterface.id;
                                        computeClient.virtualMachines.createOrUpdate("myResourceGroup", "newLinuxVM", vmParameters, (err, data) => {
                                            if (err) return console.log(err);
                                            console.log("Created new Linux VM");
                                        });
                                    });
                            });
                    });
            });
    });
```

명령줄에서 코드를 실행합니다.

```bash
node createVM.js
```

코드가 완료되면 새 가상 머신의 IP를 가져오고 코드에서 `adminPass` 값을 사용하여 SSH로 로그인합니다.

```azurecli
az vm list-ip-addresses --name newLinuxVM
```

```bash
ssh testadmin@*vm_ip_address*
```

## <a name="write-a-blob-to-azure-storage"></a>Azure Storage에 Blob 쓰기

다음 코드를 사용하여 현재 디렉터리에 *uploadFile.js* 파일을 새로 만듭니다.

```javascript
'use strict'

const MsRest = require('ms-rest-azure');
const storage = require('azure-storage');
const storageManagementClient = require('azure-arm-storage');

MsRest.loginWithServicePrincipalSecret(process.env.AZURE_ID, process.env.AZURE_PASS, process.env.AZURE_TENANT, (err, credentials) => {
    const client = new storageManagementClient(credentials, process.env.AZURE_SUB);

    const createParameters = {
        location: 'eastus',
        sku: {
            name: 'Standard_LRS'
        },
        kind: 'BlobStorage',
        accessTier: 'Hot'
    };

    const blobAccountName = "nodedemo" + Math.random().toString(10).substr(4, 7);

    client.storageAccounts.create("myResourceGroup", blobAccountName, createParameters, (err, result, httpRequest, response) => {
        if (err) console.log(err);

        // get a connection string for the account
        client.storageAccounts.listKeys("myResourceGroup", blobAccountName, (err, result) => {
            if (err) console.log(err);

            // get a storage key and use it to connect to the azure-storage module
            const blobSvc = storage.createBlobService(blobAccountName, result.keys[0].value);
            blobSvc.createContainerIfNotExists('mycontainer', { publicAccessLevel: 'blob' }, function (error, result, response) {
                if (!error) {
                    blobSvc.createBlockBlobFromText('mycontainer', 'myblob', 'Hello Azure!', function (error, result, response) {
                        if (!error) {
                            console.log("File uploaded to " + "https://" + blobAccountName + ".blob.core.windows.net/mycontainer/myblob");
                        }
                    });
                }
            });

        });
    });
});
```

명령을 실행한 다음, 출력에서 URL을 복사하여 웹 브라우저에 붙여넣어 Azure Storage에서 파일을 봅니다.

```bash
node uploadFile.js
```

## <a name="clean-up-resources"></a>리소스 정리

리소스 그룹을 삭제하여 이 가이드에서 만든 리소스를 제거합니다.

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>다음 단계

앱에서 사용할 수 있는 [Node.js 샘플 코드](https://azure.microsoft.com/resources/samples/?platform=nodejs)를 추가로 탐색합니다.

## <a name="reference"></a>참조 

[참조](/javascript/api/overview/azure/)는 모든 패키지에서 사용할 수 있습니다.

## <a name="get-help-and-give-feedback"></a>도움말 가져오기 및 피드백 제공

[Stack Overflow(영문)](https://stackoverflow.com/questions/tagged/azure+node.js)의 커뮤니티에 질문을 게시합니다. [GitHub 프로젝트](https://github.com/Azure/azure-sdk-for-node)에서 Node.js용 Azure 모듈에 대한 버그 및 열기 문제를 보고합니다.
