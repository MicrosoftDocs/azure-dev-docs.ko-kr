---
title: '자습서: Azure Portal을 사용하여 PostgreSQL을 사용하는 Django 앱 배포'
description: Azure에서 웹앱 및 PostgreSQL 데이터베이스를 프로비저닝하고 GitHub에서 앱 코드를 배포합니다.
ms.devlang: python
ms.topic: tutorial
ms.date: 10/09/2020
ms.custom: devx-track-python
ms.openlocfilehash: 77cb35d31f80b52d1e79c2650c79635dc039e72d
ms.sourcegitcommit: d5dabc6dde727ed167a9dc8a4eaaf21025b3efa8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/12/2020
ms.locfileid: "91947538"
---
# <a name="tutorial-deploy-a-django-web-app-with-postgresql-using-the-azure-portal"></a>자습서: Azure Portal을 사용하여 PostgreSQL을 사용하는 Django 웹앱 배포

Azure Portal을 사용하면 데이터 기반 Python [Django](https://www.djangoproject.com/) 웹앱을 [Azure App Service](/azure/app-service/overview#app-service-on-linux)에 배포하고, [Azure Database for PostgreSQL](/azure/postgresql/) 데이터베이스에 연결할 수 있습니다. 나중에 언제든지 확장할 수 있는 체험 가격 책정 계층으로 시작할 수 있습니다.

이 경우 웹앱 코드는 GitHub 리포지토리에서 제공되며 GitHub에서 지속적으로 배포할 수 있도록 웹앱을 구성합니다. 구성된 후에는 로컬 컴퓨터에서 추가 개발을 수행하고 리포지토리에 대한 변경 내용을 커밋할 수 있습니다. 그런 다음, Azure의 웹앱에서 이러한 변경 내용을 자동으로 배포합니다.

이 자습서에서는 Azure Portal을 사용하여 다음 작업을 완료합니다.

> [!div class="checklist"]
> - GitHub 리포지토리에서 배포되는 웹앱을 Azure에 프로비저닝합니다.
> - Azure에서 PostgreSQL 서버 및 데이터베이스 프로비저닝 및 웹앱에 연결
> - 코드를 업데이트하고 GitHub에서 자동으로 다시 배포하도록 변경 내용 커밋
> - 진단 로그 보기
> - Azure Portal에서 웹앱 관리

**[이 자습서의 Azure CLI 기반 버전](/azure/app-service/tutorial-python-postgresql-app)** 을 사용할 수도 있습니다.

## <a name="fork-the-sample-repository"></a>샘플 리포지토리 포크

브라우저에서 [https://github.com/Azure-Samples/djangoapp](https://github.com/Azure-Samples/djangoapp)으로 이동하여 리포지토리를 사용자 고유의 GitHub 계정으로 포크합니다.

이후 단계에서 코드를 변경하고 다시 배포할 수 있도록 이 리포지토리의 포크를 만듭니다.

**(선택 사항) 샘플 정보:** djangoapp 샘플에는 Django 설명서의 [첫 번째 Django 앱 작성](https://docs.djangoproject.com/en/2.1/intro/tutorial01/)에 따라 가져오는 데이터 기반 Django 설문 조사 앱이 포함되어 있습니다. 또한 이 샘플은 [Django 배포 검사 목록](https://docs.djangoproject.com/en/2.1/howto/deployment/checklist/)을 사용하여 Azure App Service와 같은 프로덕션 환경에서 실행되도록 수정됩니다. (이러한 변경은 모든 프로덕션 환경에 적용되며 Azure에만 국한되지 않습니다.)

- 프로덕션 설정은 *azuresite/production.py* 파일에 있습니다. 개발 세부 정보는 *azuresite/settings.py*에 있습니다.

- `DJANGO_ENV` 환경 변수가 "production"으로 설정되면 앱에서 프로덕션 설정을 사용합니다. 이 환경 변수는 자습서의 뒷부분에서 PostgreSQL 데이터베이스 구성에 사용된 다른 환경 변수와 함께 만듭니다.

[문제가 있나요? 알려주세요.](https://aka.ms/DjangoPortalTutorialHelp)

## <a name="provision-the-web-app-in-azure"></a>Azure에서 웹앱 프로비저닝

1. [Azure Portal](https://portal.azure.com)을 엽니다.

1. **리소스 만들기**를 선택합니다. 그러면 **새로 만들기** 페이지가 열립니다.

1. **웹앱**을 검색하여 선택합니다.

1. **웹앱 만들기** 페이지에서 다음 정보를 입력합니다.

    | 필드 | 값 |
    | --- | --- |
    | Subscription | 기본값과 다른 경우 사용하려는 구독을 선택합니다. |
    | Resource group | **새로 만들기**를 선택하고, "DjangoPostgres-Tutorial-rg"를 입력합니다. |
    | 앱 이름 | 모든 Azure에서 고유한 웹앱의 이름입니다(앱 URL은 `https://<app-name>.azurewebsites.net`임). 허용되는 문자는 `A`-`Z`, `0`-`9` 및 `-`입니다. 회사 이름과 앱 식별자를 조합하여 사용하는 것이 좋습니다. |
    | 게시 | **코드**를 선택합니다. |
    | 런타임 스택 | 드롭다운 목록에서 **Python 3.8**을 선택합니다. |
    | 지역 | 가까운 위치를 선택합니다. |
    | Linux 플랜 | 포털에서 이 필드는 리소스 그룹을 기반으로 하는 App Service 계획 이름으로 채워집니다. 이름을 변경하려면 **새로 만들기**를 선택합니다. |
    | SKU 및 크기 | 구독에서 요금이 발생하더라도 최상의 성능을 위해 기본 계획을 사용합니다. 요금 청구를 방지하려면 **크기 변경**, **개발/테스트**, **B1**(30일간 체험)을 차례로 선택한 다음, **적용**을 선택합니다. 성능 향상을 위해 나중에 계획을 확장할 수 있습니다. |

1. **검토 + 만들기**, **만들기**를 차례로 선택합니다. Azure에서 웹앱을 프로비저닝하는 데 몇 분 정도 걸립니다.

1. 프로비저닝이 완료되면 **리소스로 이동**을 선택하여 웹앱에 대한 개요 페이지를 엽니다. 이후 단계를 위해 이 브라우저 창 또는 탭을 열어 둡니다.

[문제가 있나요? 알려주세요.](https://aka.ms/DjangoPortalTutorialHelp)

## <a name="provision-the-postgresql-database-server-in-azure"></a>Azure에서 PostgreSQL 데이터베이스 서버 프로비저닝

1. [Azure Portal](https://portal.azure.com)을 사용하여 새 브라우저 창 또는 탭을 엽니다. 데이터베이스 페이지의 일부 정보를 이전 섹션에서 여전히 열려 있는 웹앱 페이지로 전송해야 하므로 데이터베이스를 프로비저닝하기 위해 새 탭을 사용합니다.

1. **리소스 만들기**를 선택합니다. 그러면 **새로 만들기** 페이지가 열립니다.

1. **Azure Database for PostgreSQL**을 검색하여 선택합니다.

1. 다음 페이지의 **단일 서버** 아래에서 **만들기**를 선택합니다.

1. **단일 서버** 페이지에서 다음 정보를 입력합니다.

    | 필드 | 값 |
    | --- | --- |
    | Subscription | 기본값과 다른 경우 사용하려는 구독을 선택합니다. |
    | Resource group | 이전 섹션에서 만든 "DjangoPostgres-Tutorial-rg" 그룹을 선택합니다. |
    | 서버 이름 | 모든 Azure에서 고유한 데이터베이스 서버의 이름입니다(데이터베이스 서버의 URL은 `https://<server-name>.postgres.database.azure.com`이 됨). 허용되는 문자는 `A`-`Z`, `0`-`9` 및 `-`입니다. 회사 이름과 서버 식별자를 조합하여 사용하는 것이 좋습니다. |
    | 데이터 원본 | **없음** |
    | 위치 | 가까운 위치를 선택합니다. |
    | 버전 | 기본값(최신 버전)을 유지합니다. |
    | 컴퓨팅 + 스토리지 | **서버 구성**을 선택한 다음, **기본** 및 **5세대**를 선택합니다. **vCore**를 1로 설정하고, **스토리지**를 5GB로 설정한 다음, **확인**을 선택합니다. 이러한 선택 항목은 Azure의 PostgreSQL에 사용할 수 있는 가장 저렴한 서버를 프로비저닝합니다. 또한 Azure 계정에 서버 비용이 포함된 크레딧이 있을 수 있습니다. |
    | 관리 사용자 이름, 암호, 암호 확인 | 데이터베이스 서버의 관리자 계정에 대한 자격 증명을 입력합니다. 이 자습서의 뒷부분에서 필요하므로 이러한 자격 증명을 기록합니다. |

1. **검토 + 만들기**를 선택한 다음, **만들기**를 선택합니다. Azure에서 웹앱을 프로비저닝하는 데 몇 분 정도 걸립니다.

1. 프로비저닝이 완료되면 **리소스로 이동**을 선택하여 데이터베이스 서버에 대한 개요 페이지를 엽니다.

[문제가 있나요? 알려주세요.](https://aka.ms/DjangoPortalTutorialHelp)

## <a name="create-the-pollsdb-database-on-the-postgresql-server"></a>PostgreSQL 서버에서 pollsdb 데이터베이스 만들기

이 섹션에서는 Azure Cloud Shell에서 데이터베이스 서버에 연결하고, PostgreSQL 명령을 사용하여 서버에서 "pollsdb" 데이터베이스를 만듭니다. 이 데이터베이스는 앱 샘플 코드에 필요합니다.

1. PostgreSQL 서버에 대한 개요 페이지의 왼쪽에 있는 **설정** 아래에서 **연결 보안**을 선택합니다.

    ![방화벽 규칙에 대한 포털 연결 보안 페이지](media/tutorial-python-postgresql-app-portal/server-firewall-rules.png)

1. **0.0.0.0 - 255.255.255.255 추가**라는 레이블이 지정된 단추를 선택하고, 표시되는 팝업 메시지에서 **계속**을 선택한 다음, 페이지 위쪽에서 **저장**을 선택합니다. 이러한 작업은 이후 섹션에서 Django 데이터 모델 마이그레이션을 실행하는 것처럼 Cloud Shell 및 SSH에서 데이터베이스 서버에 연결할 수 있도록 하는 규칙을 추가합니다.

1. 창 위쪽에서 Cloud Shell 아이콘을 선택하여 Azure Portal에서 Azure Cloud Shell을 엽니다.

    ![Azure Portal 도구 모음의 Cloud Shell 단추](media/tutorial-python-postgresql-app-portal/portal-launch-icon.png)

1. Cloud Shell에서 다음 명령을 실행합니다.

    ```bash
    psql --host=<server-name>.postgres.database.azure.com --port=5432 --username=<user-name>@<server-name> --dbname=postgres
    ```

    서버를 구성하는 경우 `<server-name>` 및 `<user-name>`을 이전 섹션에서 사용한 이름으로 바꿉니다. Postgres에 필요한 전체 사용자 이름 값은 `<user-name>@<server-name>`입니다.

    마우스 오른쪽 단추를 클릭한 다음, **붙여넣기**를 선택하여 위의 명령을 복사하여 Cloud Shell에 붙여넣을 수 있습니다.

    메시지가 표시되면 관리자 암호를 입력합니다.

1. 셸이 성공적으로 연결되면 `postgres=>`라는 프롬프트가 표시됩니다. 이 프롬프트는 "postgres"라는 기본 관리 데이터베이스에 연결되었음을 나타냅니다. ("postgres"는 앱을 사용하기 위한 데이터베이스가 아닙니다.)

1. 프롬프트에서 `CREATE DATABASE pollsdb;` 명령을 실행합니다. 명령을 완료하는 끝 세미콜론을 포함해야 합니다.

1. 데이터베이스가 성공적으로 만들어지면 명령에서 `CREATE DATABASE`를 표시합니다. 데이터베이스가 만들어졌는지 확인하려면 `\c pollsdb`를 실행합니다. 이 명령은 프롬프트를 성공을 나타내는 `pollsdb=>`로 변경합니다.

1. `exit` 명령을 실행하여 psql을 종료합니다.

[문제가 있나요? 알려주세요.](https://aka.ms/DjangoPortalTutorialHelp)

## <a name="connect-the-database"></a>데이터베이스 연결

이 섹션에서는 `pollsdb` 데이터베이스에 연결해야 하는 웹앱에 대한 설정을 만듭니다. 이러한 설정은 앱 코드에서 환경 변수로 표시됩니다. (자세한 내용은 [환경 변수 액세스](/azure/app-service/configure-language-python#access-environment-variables)를 참조하세요.)

1. 이전 섹션에서 만든 웹앱에 대한 브라우저 탭 또는 창으로 다시 전환합니다.

1. 왼쪽의 **설정** 아래에서 **구성**을 선택한 다음, 페이지 위쪽에서 **애플리케이션 설정**을 선택합니다.

    ![웹앱에 대한 포털 설정 구성](media/tutorial-python-postgresql-app-portal/web-app-settings.png)

1. **새 애플리케이션 설정** 단추를 사용하여 djangoapp 샘플에서 기대하는 다음 각 값에 대한 설정을 만듭니다.

    | 설정 이름 | 값 |
    | --- | --- |
    | DJANGO_ENV | `production`(이 값은 [샘플 개요](#fork-the-sample-repository)의 앞부분에서 설명한 대로 프로덕션 구성을 사용하도록 앱에 지시합니다.) |
    | DBHOST | 이전 섹션의 데이터베이스 서버 이름입니다. 즉, `.postgres.database.azure.com` 앞에 오는 서버 URL의 `<server-name>` 부분입니다. (*azuresite/production.py*의 코드는 전체 URL을 자동으로 생성합니다.) |
    | DBNAME | `pollsdb` |
    | DBUSER | 데이터베이스를 프로비저닝할 때 사용되는 관리자 사용자 이름입니다. (샘플 코드는 `@<server-name>` 부분을 자동으로 추가합니다. *azuresite/production.py*를 참조하세요.) |
    | DBPASS | 이전에 만든 관리자 암호입니다. |

1. **저장**을 선택한 다음, **계속**을 선택하여 설정을 적용합니다.

    > [!IMPORTANT]
    > 설정이 변경되면 반드시 **저장**을 선택해야 합니다. **새 애플리케이션 설정** 단추를 사용하여 만든 모든 설정은 **저장**을 선택할 때까지 적용되지 않습니다.

[문제가 있나요? 알려주세요.](https://aka.ms/DjangoPortalTutorialHelp)

## <a name="deploy-app-code-to-the-web-app-from-a-repository"></a>리포지토리에서 웹앱으로 앱 코드 배포

데이터베이스 및 연결 설정이 준비되면 이제 GitHub 리포지토리에서 코드를 직접 배포하도록 웹앱을 구성할 수 있습니다.

1. 웹앱에 대한 브라우저 창 또는 탭의 왼쪽에 있는 **배포** 아래에서 **배포 센터**를 선택합니다.

1. **원본 제어** 단계에서 **GitHub**를 선택한 다음, 필요한 경우 **권한 부여**를 선택합니다. 그런 다음, 로그인 프롬프트를 따르거나 **계속**을 선택하여 현재 GitHub 로그인을 사용합니다.

1. **빌드 공급자** 단계에서 **App Service 빌드 서비스**, **계속**을 차례로 선택합니다.

1. **구성** 단계에서 다음 값을 선택합니다.

    | 필드 | 값 |
    | --- | --- |
    | 조직 | 샘플 리포지토리를 포크한 GitHub 계정 |
    | 리포지토리 | djangoapp |
    | Branch | master |

1. **계속**을 선택하여 리포지토리를 선택한 다음, **마침**을 선택합니다. Azure에서 몇 초 내에 코드를 배포하고 앱을 시작합니다.

    App Service는 각 하위 폴더에서 *wsgi.py* 파일을 찾아서 Django 프로젝트를 검색합니다. App Service에서 해당 파일을 찾으면 Django 웹앱이 로드됩니다. 자세한 내용은 [기본 제공 Python 이미지 구성](/azure/app-service/configure-language-python)을 참조하세요.

[문제가 있나요? 알려주세요.](https://aka.ms/DjangoPortalTutorialHelp)

## <a name="run-django-database-migrations"></a>Django 데이터베이스 마이그레이션 실행

코드가 배포되고 데이터베이스가 준비되면 앱을 거의 사용할 수 있습니다. 이제 데이터베이스 자체에 필요한 스키마를 설정하기만 하면 됩니다. 이렇게 하려면 Django 앱의 데이터 모델을 데이터베이스로 "마이그레이션"합니다.

1. 웹 앱의 브라우저 창 또는 탭에서 **SSH**(왼쪽에 있는 **개발 도구** 아래에서)를 선택한 다음, **이동**하여 웹앱 서버에서 SSH 콘솔을 엽니다. 웹앱 컨테이너에서 시작해야 하므로 처음 연결하는 데 1분 정도 걸릴 수 있습니다.

1. 콘솔에서 웹앱의 폴더로 변경합니다.

    ```bash
    cd site/wwwroot
    ```

1. 컨테이너의 가상 환경을 활성화합니다.

    ```bash
    source /antenv/bin/activate
    ```

1. Python 패키지를 설치합니다.

    ```bash
    pip install -r requirements.txt
    ```

1. 데이터베이스 마이그레이션을 실행합니다.

    ```bash
    python manage.py migrate
    ```

1. 앱에 대한 관리자 로그인을 만듭니다.

    ```bash
    python manage.py createsuperuser
   ```

    `createsuperuser` 명령은 웹앱 내에서 사용되는 Django 슈퍼 사용자(또는 관리자) 자격 증명을 요구하는 메시지를 표시합니다. 이 자습서에서는 기본 사용자 이름으로 `root`를 사용하고, **Enter** 키를 눌러 이메일 주소를 비워 두고, 암호로 `Pollsdb1`을 입력합니다.

[문제가 있나요? 알려주세요.](https://aka.ms/DjangoPortalTutorialHelp)

### <a name="create-a-poll-question-in-the-app"></a>앱에서 설문 조사 질문 만들기

이제 PostgreSQL 데이터베이스를 사용하고 있음을 보여 주기 위해 앱에 대한 빠른 테스트를 실행할 준비가 되었습니다.

1. 웹앱에 대한 브라우저 창 또는 탭에서 **개요** 페이지로 돌아간 다음, 웹앱에 대한 **URL**(`http://<app-name>.azurewebsites.net` 형식)을 선택합니다.

1. 데이터베이스에 아직 특정 설문 조사가 없으므로 앱에서 "설문 조사를 사용할 수 없습니다."라는 메시지가 표시됩니다.

1. `http://<app-name>.azurewebsites.net/admin`("Django 관리" 페이지)으로 이동하여 이전 섹션의 슈퍼 사용자 자격 증명(`root` 및 `Pollsdb1`)을 사용하여 로그인합니다.

1. **설문 조사** 아래에서 **질문** 옆에 있는 **추가**를 선택하고, 몇 가지 선택 항목이 있는 설문 조사 질문을 만듭니다.

1. `http://<app-name>.azurewebsites.net/`으로 다시 이동하여 이제 질문이 사용자에게 표시되는지 확인합니다. 데이터베이스에 일부 데이터를 생성하려는 질문에 대답합니다.

**축하합니다.** Linux용 Azure App Service에서 활성 PostgreSQL 데이터베이스를 사용하는 Python Django 웹앱이 실행되고 있습니다.

[문제가 있나요? 알려주세요.](https://aka.ms/DjangoPortalTutorialHelp)

## <a name="update-the-app-and-redeploy"></a>앱 업데이트 및 재배포

이 자습서의 앞부분에서 설명한 대로 변경 내용을 GitHub 리포지토리에 커밋할 때마다 Azure에서 앱 코드를 다시 배포합니다.

그러나 Django 앱의 데이터 모델을 변경하는 경우 해당 변경 내용을 데이터베이스로 마이그레이션해야 합니다.

1. [Django 데이터베이스 마이그레이션 실행](#run-django-database-migrations)에서 설명한 대로 SSH를 통해 웹앱에 다시 연결합니다.

1. `cd site/wwwroot`를 사용하여 앱 폴더로 변경합니다.

1. `source /antenv/bin/activate`를 사용하여 가상 환경을 활성화합니다.

1. `python manage.py migrate`를 사용하여 마이그레이션을 다시 실행합니다.

[문제가 있나요? 알려주세요.](https://aka.ms/DjangoPortalTutorialHelp)

## <a name="view-diagnostic-logs"></a>진단 로그 보기

Azure에서 앱을 호스팅하는 컨테이너 내에서 생성된 콘솔 로그에 액세스할 수 있습니다.

Azure Portal의 웹앱 페이지 왼쪽에 있는 **모니터링** 아래에서 **로그 스트림**을 선택합니다. 로그가 콘솔 출력으로 표시됩니다.

`https://<app-name>.scm.azurewebsites.net/api/logs/docker`의 브라우저에서 로그 파일을 검사할 수도 있습니다.

[문제가 있나요? 알려주세요.](https://aka.ms/DjangoPortalTutorialHelp)

## <a name="clean-up-resources"></a>리소스 정리

추가 개발 작업에 필요한 경우 실행 중인 앱 및 데이터베이스를 그대로 둘 수 있습니다. 그렇지 않은 경우 요금이 지속적으로 청구되지 않도록 이 자습서에서 만든 리소스 그룹을 삭제합니다. 그러면 해당 그룹에 포함된 리소스도 모두 삭제됩니다.

1. Azure Portal의 창 위쪽에 있는 검색 창에서 "DjangoPostgres-Tutorial-rg"를 입력한 다음, **리소스 그룹** 아래에서 동일한 이름을 선택합니다.

1. 리소스 그룹 페이지에서 **리소스 그룹 삭제**를 선택합니다.

1. 프롬프트가 표시되면 리소스 그룹의 이름을 입력하고, **삭제**를 선택합니다.

[문제가 있나요? 알려주세요.](https://aka.ms/DjangoPortalTutorialHelp)

## <a name="next-steps"></a>다음 단계

App Service에서 Python 앱을 실행하는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [Python 앱 구성](/azure/app-service/configure-language-python)
