---
title: 빠른 시작 - Chef를 사용하여 Azure에서 Windows 가상 머신 구성
description: 이 빠른 시작에서는 Chef를 사용하여 Azure에서 Windows 가상 머신을 배포하고 구성하는 방법을 알아봅니다.
keywords: Chef, Azure, DevOps, 가상 머신
ms.topic: tutorial
ms.service: chef
author: tomarchermsft
ms.author: tarcher
ms.date: 02/22/2020
ms.custom: devx-track-chef
ms.openlocfilehash: 7afddc83fef8e52e074600df75f2a2f6bc7c9ea7
ms.sourcegitcommit: 815cf2acff71e849735f7afce54723f03ffa5df3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/17/2020
ms.locfileid: "88501359"
---
# <a name="quickstart---configure-a-windows-virtual-machine-in-azure-using-chef"></a>빠른 시작 - Chef를 사용하여 Azure에서 Windows 가상 머신 구성

Chef를 사용하면 자동화 및 필요한 상태 구성을 제공할 수 있습니다.

최신 클라우드 API 릴리스에서 Chef는 Azure와 원활한 통합을 통해 단일 명령으로 구성 상태를 프로비전 및 배포할 수 있는 기능을 제공합니다.

이 문서에서는 Azure 가상 머신을 프로비저닝하고, 정책 또는 쿡북을 만든 다음, 이 쿡북을 Azure 가상 머신에 배포하는 과정을 안내하도록 Chef 환경을 설정합니다.

## <a name="chef-basics"></a>Chef 기본 사항

이 문서를 시작하기 전에 [Chef에 대한 기본 개념을 검토](https://www.chef.io/chef)하세요.

다음 다이어그램은 대략적인 Chef 아키텍처를 보여줍니다.

![Chef 아키텍처](media/windows-vm-configure/chef-architecure.png)

Chef는 세 가지 주요 아키텍처 구성 요소인 
- Chef Server - 관리 지점이며, 호스트형 솔루션과 온-프레미스 솔루션의 두 가지 옵션이 있습니다.
- Chef Client(노드) - 관리하는 서버에 있는 에이전트입니다.
- Chef Workstation - 관리 워크스테이션(정책을 만들기고 관리 명령을 실행하는 위치)과 Chef 도구의 소프트웨어 패키지 모두의 이름입니다.

일반적으로 **워크스테이션**은 명령을 실행하는 위치로 표시되고, **Chef Workstation**은 소프트웨어 패키지로 표시됩니다.

예를 들어 **Chef Workstation**의 일부로 knife 명령을 다운로드하지만, **워크스테이션**에서 knife 명령을 실행하여 인프라를 관리합니다.

또한 Chef는 *쿡북* 및 *레시피*의 개념을 사용합니다. 이러한 용어는 각각 서버에 정의되고 적용되는 정책입니다.

## <a name="preparing-your-workstation"></a>워크스테이션 준비

먼저 Chef 구성 파일과 Cookbook을 저장할 디렉터리를 만들어 워크스테이션을 준비합니다.

이름이 `C:\Chef`인 디렉터리를 만듭니다.

최신 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) 버전을 워크스테이션에 다운로드하여 설치합니다.

## <a name="configure-azure-service-principal"></a>Azure 서비스 주체 구성

Chef Workstation에서 Azure 리소스를 만드는 데 도움이 되는 서비스 주체를 사용합니다.  필요한 권한을 사용하여 관련 서비스 주체를 만들려면 PowerShell에서 다음 명령을 실행합니다.

```powershell
Login-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName "<yourSubscriptionName>"
$myApplication = New-AzureRmADApplication -DisplayName "automation-app" -HomePage "https://chef-automation-test.com" -IdentifierUris "https://chef-automation-test.com" -Password "#1234p$wdchef19"
New-AzureRmADServicePrincipal -ApplicationId $myApplication.ApplicationId
New-AzureRmRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $myApplication.ApplicationId
```

필요한 SubscriptionID, TenantID, ClientID 및 클라이언트 암호(이 자습서에서 이전에 설정한 암호)의 값을 적어 둡니다. 

## <a name="configure-chef-server"></a>Chef Server 구성

이 가이드에서는 사용자가 Hosted Chef에 가입한다고 가정합니다.

Chef Server를 아직 사용하고 있지 않으면 다음을 수행할 수 있습니다.

* [Hosted Chef](https://manage.chef.io/signup)에 등록합니다. 이렇게 하는 것이 Chef를 시작하는 가장 빠른 방법입니다.
* [Chef Docs](https://docs.chef.io/)의 [설치 지침](https://docs.chef.io/install_server.html)에 따라 Linux 기반 컴퓨터에 독립 실행형 Chef Server를 설치합니다.

### <a name="create-a-hosted-chef-account"></a>호스팅된 Chef 계정 만들기

[여기](https://manage.chef.io/signup)에서 Hosted Chef 계정을 등록합니다.

등록 프로세스 중에 새 조직을 만들지 묻는 메시지가 나타납니다.

![조직 만들기 창](media/windows-vm-configure/create-organization.png)

조직을 만든 후 시작 키트를 다운로드합니다.

![Chef 구성](media/windows-vm-configure/configure-chef.png)

> [!NOTE]
> 키가 다시 설정된다는 경고 메시지가 표시되는 경우 기존 인프라를 아직 구성하지 않았으므로 계속 진행할 수 있습니다.
>

이 시작 키트 zip 파일의 `.chef` 디렉터리에는 조직 구성 파일 및 사용자 키가 포함되어 있습니다.

`organization-validator.pem`은 프라이빗 키이며 프라이빗 키는 Chef Server에 저장되지 않아야 하므로 별도로 다운로드해야 합니다. [Chef Manage(Chef 관리)](https://manage.chef.io/)에서 Administration(관리) 섹션으로 이동하여 "Reset Validation Key(유효성 검사 키 다시 설정)"를 선택합니다. 그러면 별도로 다운로드할 수 있는 파일이 제공됩니다. 파일을 c:\chef에 저장합니다.

### <a name="configure-a-chef-workstation"></a>Chef 워크스테이션 구성

`chef-starter.zip`의 내용을 `c:\chef`로 추출합니다.

`chef-starter\chef-repo\.chef`의 모든 파일을 `c:\chef` 디렉터리에 복사합니다.

`c:\Downloads`에 저장된 경우 `organization-validator.pem` 파일을 `c:\chef`에 복사합니다.

이제 디렉터리가 다음 예제와 같이 표시됩니다.

```powershell

Directory: C:\Users\username\chef

Mode           LastWriteTime    Length Name
----           -------------    ------ ----
d-----    12/6/2018   6:41 PM           .chef
d-----    12/6/2018   5:40 PM           chef-repo
d-----    12/6/2018   5:38 PM           cookbooks
d-----    12/6/2018   5:38 PM           roles
-a----    12/6/2018   5:38 PM       495 .gitignore
-a----    12/6/2018   5:37 PM      1678 azuredocs-validator.pem
-a----    12/6/2018   5:38 PM      1674 user.pem
-a----    12/6/2018   5:53 PM       414 knife.rb
-a----    12/6/2018   5:38 PM      2341 README.md
```

이제 c:\chef의 루트에는 5개의 파일과 4개의 디렉터리(빈 chef-repo 디렉터리 포함)가 있습니다.

### <a name="edit-kniferb"></a>knife.rb 편집

PEM 파일에는 조직 및 관리자의 통신용 프라이빗 키가 들어 있고, knife.rb 파일에는 knife 구성이 들어 있습니다. knife.rb 파일을 편집해야 합니다.

원하는 편집기에서 knife.rb 파일을 엽니다. 변경되지 않은 파일은 다음과 같습니다.

```rb
current_dir = File.dirname(__FILE__)
log_level           :info
log_location        STDOUT
node_name           "mynode"
client_key          "#{current_dir}/user.pem"
chef_server_url     "https://api.chef.io/organizations/myorg"
cookbook_path       ["#{current_dir}/cookbooks"]
```

다음 정보를 knife.rb에 추가하고, 자리 표시자를 사용자의 정보로 바꿉니다.

```rb
validation_client_name   "myorg-validator"
validation_key           "#{current_dir}/myorg.pem"
knife[:azure_tenant_id] =         "0000000-1111-aaaa-bbbb-222222222222"
knife[:azure_subscription_id] =   "11111111-bbbbb-cccc-1111-222222222222"
knife[:azure_client_id] =         "11111111-bbbbb-cccc-1111-2222222222222"
knife[:azure_client_secret] =     "#1234p$wdchef19"
```

이러한 줄은 Knife에서 `c:\chef\cookbooks` 아래의 쿡북 디렉터리를 참조할 수 있도록 합니다.

이제 `knife.rb` 파일은 다음 예제와 같이 표시됩니다.

![Knife 파일 예제](./media/windows-vm-configure/knife-file-example.png)

```rb
current_dir = File.dirname(__FILE__)
log_level                :info
log_location             STDOUT
node_name                "myorg"
client_key               "#{current_dir}/myorg.pem"
validation_client_name   "myorg-validator"
validation_key           "#{current_dir}/myorg-validator.pem"
chef_server_url          "https://api.chef.io/organizations/myorg"
cookbook_path            ["#{current_dir}/../cookbooks"]
knife[:azure_tenant_id] = "0000000-1111-aaaa-bbbb-222222222222"
knife[:azure_subscription_id] = "11111111-bbbbb-cccc-1111-222222222222"
knife[:azure_client_id] = "11111111-bbbbb-cccc-1111-2222222222222"
knife[:azure_client_secret] = "#1234p$wdchef19"
```

## <a name="install-chef-workstation"></a>Chef Workstation 설치

다음으로, Chef Workstation을 [다운로드 및 설치](https://downloads.chef.io/chef-workstation/)합니다.

Chef Workstation을 기본 위치에 설치합니다.

바탕 화면에 CW PowerShell이 표시됩니다. 이 도구는 Chef 제품과 상호 작용하는 데 사용됩니다. CW PowerShell은 `chef-run` 및 Chef CLI 명령(예: `chef`)과 같은 새 명령을 사용할 수 있도록 합니다. `chef -v`를 사용하여 설치된 Chef Workstation 및 Chef 도구 버전을 확인합니다. Chef Workstation 앱에서 **About Chef Workstation(Chef Workstation 정보)** 을 선택하여 Workstation 버전을 확인할 수도 있습니다.

`chef --version`은 다음과 같은 결과를 반환합니다.

```powershell
Chef Workstation: 0.4.2
  chef-run: 0.3.0
  chef-client: 15.0.300
  delivery-cli: 0.0.52 (9d07501a3b347cc687c902319d23dc32dd5fa621)
  berks: 7.0.8
  test-kitchen: 2.2.5
  inspec: 4.3.2
```

> [!NOTE]
> 경로 순서가 중요합니다! opscode 경로가 올바른 순서로 있지 않으면 문제가 발생합니다.
>

계속하기 전에 워크스테이션을 다시 부팅하세요.

### <a name="install-knife-azure"></a>Knife Azure 설치

이 자습서는 Azure Resource Manager를 사용하여 가상 머신과 상호 작용한다고 가정합니다.

Azure 플러그 인이 포함된 Knife Azure 확장을 설치합니다.

다음 명령을 실행합니다.

```bash
chef gem install knife-azure ––pre
```

> [!NOTE]
> `–-pre` 인수는 최신 API 세트에 대한 액세스를 제공하는 최신 RC 버전의 Knife Azure 플러그 인을 받을 수 있도록 합니다.
>
>

많은 종속성이 동시에 설치될 수도 있습니다.

![knife-azure 설치의 출력](./media/windows-vm-configure/install-knife-azure.png)

모든 것이 올바르게 구성되었는지 확인하려면 다음 명령을 실행합니다.

```bash
knife azurerm server list
```

모든 것이 올바르게 구성되었으면 사용 가능한 Azure 이미지 목록이 표시됩니다.

축하합니다. 워크스테이션이 설정되었습니다.

## <a name="create-a-chef-cookbook"></a>Chef 쿡북 만들기

Chef에서는 쿡북을 사용하여 관리 클라이언트에서 실행할 명령 세트를 정의합니다. 쿡북은 간단하게 만들 수 있습니다. 즉 `chef generate cookbook` 명령을 사용하여 쿡북 템플릿을 만들기만 하면 됩니다. 이 Cookbook은 IIS를 자동으로 배포하는 웹 서버용입니다.

`C:\Chef directory` 아래에서 다음 명령을 실행합니다.

```bash
chef generate cookbook webserver
```

이 명령은 C:\Chef\cookbooks\webserver 디렉터리 아래에 파일 세트를 생성합니다. 다음으로, Chef Client에서 관리 가상 머신에 실행할 명령 세트를 정의합니다.

명령은 default.rb 파일에 저장되어 있습니다. 이 파일에서 IIS를 설치하고, IIS를 시작하며, 템플릿 파일을 `wwwroot` 폴더에 복사하는 명령 세트를 정의합니다.

C:\chef\cookbooks\webserver\recipes\default.rb 파일을 수정하고 다음 줄을 추가합니다.

```powershell
powershell_script 'Install IIS' do
        action :run
        code 'add-windowsfeature Web-Server'
end

service 'w3svc' do
        action [ :enable, :start ]
end

template 'c:\inetpub\wwwroot\Default.htm' do
        source 'Default.htm.erb'
        rights :read, 'Everyone'
end
```

작업이 완료되면 파일을 한 번 저장합니다.

## <a name="create-a-template"></a>템플릿 만들기

이 단계에서는 `default.html` 페이지로 사용할 템플릿 파일을 생성합니다.

다음 명령을 실행하여 템플릿을 생성합니다.

```bash
chef generate template webserver Default.htm
```

`C:\chef\cookbooks\webserver\templates\default\Default.htm.erb` 파일로 이동합니다. 간단한 *Hello World* HTML 코드를 추가하여 파일을 편집한 다음, 파일을 저장합니다.

## <a name="upload-the-chef-cookbook-to-the-chef-server"></a>Chef Server에 쿡북 업로드

이 단계에서는 로컬 머신에서 만든 쿡북의 복사본을 만들어 Chef Hosted Server에 업로드합니다. 업로드가 완료되면 쿡북이 **Policy(정책)** 탭 아래에 표시됩니다.

```bash
knife cookbook upload webserver
```

![쿡북을 Chef Server에 설치한 결과](./media/windows-vm-configure/cookbook-installation-under-policy-tab.png)

## <a name="deploy-a-virtual-machine-with-knife-azure"></a>Knife Azure를 사용하여 가상 머신 배포

Azure 가상 머신을 배포하고, `knife` 명령을 사용하여 `Webserver` 쿡북을 적용합니다.

`knife` 명령은 IIS 웹 서비스 및 기본 웹 페이지도 설치합니다.

```bash
knife azurerm server create `
--azure-resource-group-name rg-chefdeployment `
--azure-storage-account store `
--azure-vm-name chefvm `
--azure-vm-size 'Standard_DS2_v2' `
--azure-service-location 'westus' `
--azure-image-reference-offer 'WindowsServer' `
--azure-image-reference-publisher 'MicrosoftWindowsServer' `
--azure-image-reference-sku '2016-Datacenter' `
--azure-image-reference-version 'latest' `
-x myuser -P myPassword123 `
--tcp-endpoints '80,3389' `
--chef-daemon-interval 1 `
-r "recipe[webserver]"
```

`knife` 명령 예제에서는 Windows Server 2016이 미국 서부 지역 내에 설치된 *Standard_DS2_v2* 가상 머신을 만듭니다. 앱 요구 사항에 따라 이러한 값을 수정합니다.

명령이 실행되면 Azure Portal로 이동하여 머신에서 프로비저닝을 시작하는지 확인합니다.

![프로비저닝되는 가상 머신](./media/windows-vm-configure/virtual-machine-being-provisioned.png)

명령 프롬프트가 다음에 나타납니다.

![가상 머신을 만들 때의 Knife 출력](./media/windows-vm-configure/knife-output-when-creating-vm.png)

배포가 완료되면 새 가상 머신의 공용 IP 주소가 표시됩니다. 새 웹 사이트를 보려면 이 값을 웹 브라우저에 붙여넣습니다. 가상 머신을 배포할 때 80 포트를 열어 외부에서 사용할 수 있게 되었습니다.

![가상 머신 테스트](./media/windows-vm-configure/vm-test.png)

이 예제에서는 creative HTML 코드를 사용합니다.

노드의 상태도 [Chef Manage](https://manage.chef.io/)에서 확인할 수 있습니다.

![노드 상태 보기](./media/windows-vm-configure/view-node-status.png)

Azure Portal의 RDP 세션에서 3389 포트를 통해 연결할 수도 있습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Chef on Azure](/azure/chef/)