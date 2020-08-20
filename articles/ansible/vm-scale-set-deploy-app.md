---
title: 자습서 - Azure에서 Ansible을 사용하여 가상 머신 확장 집합에 앱 배포
description: Ansible을 사용하여 가상 머신 확장 집합을 구성하고 확장 집합에 애플리케이션을 배포하는 방법 알아보기
keywords: ansible, azure, devops, bash, 플레이북, 가상 머신, 가상 머신 확장 집합, vmss
ms.topic: tutorial
ms.date: 01/13/2020
ms.custom: devx-track-ansible
ms.openlocfilehash: 161592c9f437de0f72204051b222c7ed8c5f99e0
ms.sourcegitcommit: 16ce1d00586dfa9c351b889ca7f469145a02fad6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88240845"
---
# <a name="tutorial-deploy-apps-to-virtual-machine-scale-sets-in-azure-using-ansible"></a>자습서: Azure에서 Ansible을 사용하여 가상 머신 확장 집합에 앱 배포

[!INCLUDE [ansible-27-note.md](includes/ansible-27-note.md)]

[!INCLUDE [open-source-devops-intro-vm-scale-set.md](../includes/open-source-devops-intro-vm-scale-set.md)]

[!INCLUDE [ansible-tutorial-goals.md](includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Azure VM 그룹의 호스트 정보 검색
> * 샘플 앱 복제 및 빌드
> * 확장 집합에 JRE(Java Runtime Environment) 설치
> * 확장 집합에 Java 애플리케이션 배포

## <a name="prerequisites"></a>사전 요구 사항

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)] 
[!INCLUDE [ansible-prereqs-vm-scale-set.md](includes/ansible-prereqs-vm-scale-set.md)]
- **git** - [git](https://git-scm.com)는 이 자습서에 사용되는 Java 샘플을 다운로드하는 데 사용됩니다.
- **JDK(Java SE Development Kit)** - [JDK](https://aka.ms/azure-jdks)는 샘플 Java 프로젝트를 빌드하는 데 사용됩니다.
- **Apache Maven** - [Apache Maven](https://maven.apache.org/download.cgi)은 샘플 Java 프로젝트를 빌드하는 데 사용됩니다.

## <a name="get-host-information"></a>호스트 정보 가져오기

이 섹션의 플레이북 코드는 가상 머신 그룹의 호스트 정보를 검색합니다. 코드는 지정된 리소스 그룹 내에서 공용 IP 주소 및 부하 분산 장치를 가져오고, 인벤토리에 `scalesethosts`라는 호스트 그룹을 만듭니다.

다음 샘플 플레이북을 `get-hosts-tasks.yml`로 저장합니다.

  ```yml
  - name: Get facts for all Public IPs within a resource groups
    azure_rm_publicipaddress_facts:
      resource_group: "{{ resource_group }}"
    register: output_ip_address

  - name: Get loadbalancer info
    azure_rm_loadbalancer_facts:
      resource_group: "{{ resource_group }}"
      name: "{{ loadbalancer_name }}"
    register: output

  - name: Add all hosts
    add_host:
      groups: scalesethosts
      hostname: "{{ output_ip_address.publicipaddresses[0].ip_address }}_{{ item.properties.frontendPort }}"
      ansible_host: "{{ output_ip_address.publicipaddresses[0].ip_address }}"
      ansible_port: "{{ item.properties.frontendPort }}"
      ansible_ssh_user: "{{ admin_username }}"
      ansible_ssh_pass: "{{ admin_password }}"
    with_items:
      - "{{ output.ansible_info.azure_loadbalancers[0].properties.inboundNatRules }}"
  ```

## <a name="prepare-an-application-for-deployment"></a>배포를 위한 애플리케이션 준비

이 섹션의 플레이북 코드는 `git`을 사용하여 GitHub에서 Java 샘플 프로젝트를 복제하고 프로젝트를 빌드합니다. 

다음 플레이북을 `app.yml`로 저장합니다.

  ```yml
  - hosts: localhost
    vars:
      repo_url: https://github.com/spring-guides/gs-spring-boot.git
      workspace: ~/src/helloworld

    tasks:
    - name: Git Clone sample app
      git:
        repo: "{{ repo_url }}"
        dest: "{{ workspace }}"

    - name: Build sample app
      shell: mvn package chdir="{{ workspace }}/complete"
  ```

다음 명령을 사용하여 샘플 Ansible 플레이북을 실행합니다.

  ```bash
  ansible-playbook app.yml
  ```

플레이북을 실행하면 다음 결과와 유사한 출력이 표시됩니다.

  ```Output
  PLAY [localhost] 

  TASK [Gathering Facts] 
  ok: [localhost]

  TASK [Git Clone sample app] 
  changed: [localhost]

  TASK [Build sample app] 
  changed: [localhost]

  PLAY RECAP 
  localhost                  : ok=3    changed=2    unreachable=0    failed=0

  ```

## <a name="deploy-the-application-to-a-scale-set"></a>확장 집합에 애플리케이션 배포

이 섹션의 플레이북 코드는 다음 용도에 사용됩니다.

* `saclesethosts` 호스트 그룹에 JRE 설치
* `saclesethosts` 호스트 그룹에 Java 애플리케이션 배포

다음과 같은 두 가지 방법으로 샘플 플레이북을 가져올 수 있습니다.

* [플레이북을 다운로드](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss/vmss-setup-deploy.yml)하여 `vmss-setup-deploy.yml`에 저장합니다.
* `vmss-setup-deploy.yml`이라는 새 파일을 만들고 다음 콘텐츠를 이 파일에 복사합니다.

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    scaleset_name: myScaleSet
    loadbalancer_name: myScaleSetLb
    admin_username: azureuser
    admin_password: "{{ admin_password }}"
  tasks:
  - include: get-hosts-tasks.yml

- name: Install JRE on a scale set
  hosts: scalesethosts
  become: yes
  vars:
    workspace: ~/src/helloworld
    admin_username: azureuser

  tasks:
  - name: Install JRE
    apt:
      name: default-jre
      update_cache: yes

  - name: Copy app to Azure VM
    copy:
      src: "{{ workspace }}/complete/target/gs-spring-boot-0.1.0.jar"
      dest: "/home/{{ admin_username }}/helloworld.jar"
      force: yes
      mode: 0755

  - name: Start the application
    shell: java -jar "/home/{{ admin_username }}/helloworld.jar" >/dev/null 2>&1 &
    async: 5000
    poll: 0
```

플레이북을 실행하기 전에 다음 정보를 참조하세요.

* `vars` 섹션에서 `{{ admin_password }}` 자리 표시자를 해당 암호로 바꿉니다.
* 암호에 ssh 연결 형식을 사용하려면 다음과 같이 sshpass 프로그램을 설치합니다.

    Ubuntu:

    ```bash
    apt-get install sshpass
    ```

    CentOS:

    ```bash
    yum install sshpass
    ```

* 일부 환경에서는 키 대신 SSH 암호를 사용하면 오류가 발생할 수 있습니다. 이 오류가 수신되면 `/etc/ansible/ansible.cfg` 또는 `~/.ansible.cfg`에 다음 줄을 추가하여 호스트 키 확인을 해제할 수 있습니다.

    ```bash
    [defaults]
    host_key_checking = False
    ```

다음 명령을 사용하여 플레이북을 실행합니다.

  ```bash
  ansible-playbook vmss-setup-deploy.yml
  ```

Ansible 플레이북 명령 실행의 출력은 샘플 Java 애플리케이션이 확장 집합의 호스트 그룹에 설치되었음을 나타냅니다.

  ```Output
  PLAY [localhost]

  TASK [Gathering Facts]
  ok: [localhost]

  TASK [Get facts for all Public IPs within a resource groups]
  ok: [localhost]

  TASK [Get loadbalancer info]
  ok: [localhost]

  TASK [Add all hosts]
  changed: [localhost] ...

  PLAY [Install JRE on scale set]

  TASK [Gathering Facts]
  ok: [40.114.30.145_50000]
  ok: [40.114.30.145_50003]

  TASK [Copy app to Azure VM]
  changed: [40.114.30.145_50003]
  changed: [40.114.30.145_50000]

  TASK [Start the application]
  changed: [40.114.30.145_50000]
  changed: [40.114.30.145_50003]

  PLAY RECAP
  40.114.30.145_50000        : ok=4    changed=3    unreachable=0    failed=0
  40.114.30.145_50003        : ok=4    changed=3    unreachable=0    failed=0
  localhost                  : ok=4    changed=1    unreachable=0    failed=0
  ```

## <a name="verify-the-results"></a>결과 확인

확장 집합에 대한 부하 분산 장치의 URL로 이동하여 작업 결과를 확인합니다.

![Azure의 확장 집합에서 실행되는 Java 앱](media/vm-scale-set-deploy-app/demo.png)

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [자습서: Azure에서 Ansible을 사용하여 가상 머신 확장 집합 자동 크기 조정](./vm-scale-set-auto-scale.md)