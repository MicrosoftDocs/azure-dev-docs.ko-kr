---
title: Java 웹앱 샘플용 Azure 관리 라이브러리
description: Java용 Azure 관리 라이브러리를 사용하여 App Service에서 호스팅되는 Azure 웹앱을 만들고 업데이트하기 위한 샘플 코드를 얻습니다.
keywords: Azure, Java, SDK, API, Maven, Gradle, 웹앱, App Service
author: rloutlaw
ms.date: 04/16/2017
ms.topic: article
ms.service: multiple
ms.assetid: 43633e5c-9fb1-4807-ba63-e24c126754e2
ms.custom: seo-java-august2019, seo-java-september2019, devx-track-java
ms.openlocfilehash: d28fb164b4b7ba862e5297225bd34ac2f93c1a54
ms.sourcegitcommit: cbcde17e91e7262a596d813243fd713ce5e97d06
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/06/2020
ms.locfileid: "93405812"
---
# <a name="azure-management-libraries-for-java---web-app-samples"></a>Java - 웹앱 샘플용 Azure 관리 라이브러리 

다음 표는 웹앱을 만들고 구성하는 데 사용할 수 있는 Java 소스에 연결됩니다.

| 샘플 | Description |
|---|---|
| **앱 만들기** ||
| [웹앱 만들기 및 FTP 또는 GitHub에서 코드 배포][1] | 로컬 Git, FTP 및 GitHub의 연속 통합에서 웹앱을 배포합니다. |
| [웹앱 만들기 및 배포 슬롯 관리][2] | 웹앱을 만들고, 스테이징 슬롯에 배포한 다음, 슬롯 간에 배포를 교환합니다. |
| **앱 구성** ||
| [웹앱 만들기 및 사용자 지정 도메인 구성][3] | 사용자 지정 도메인 및 자체 서명된 SSL 인증서가 있는 웹앱을 만듭니다. |
| **앱 스케일링** ||
| [여러 지역에서 항상 사용 가능한 웹앱 크기 조정][4] | Azure Traffic Manager를 사용하여 서로 다른 세 지역의 웹앱 크기를 조정하고 단일 엔드포인트를 통해 사용할 수 있게 합니다. | 
| **리소스에 앱 연결** ||
| [스토리지 계정에 웹앱 연결][5] | Azure Storage 계정을 만들고, 앱 설정에 스토리지 계정 연결 문자열을 추가합니다. |
| [SQL 데이터베이스에 웹앱 연결][6] | 웹앱 및 SQL 데이터베이스를 만들고, 앱 설정에 SQL 데이터베이스 연결 문자열을 추가합니다. |

[1]: ./index.yml
[2]: https://github.com/Azure-Samples/app-service-java-manage-staging-and-production-slots-for-web-apps/
[3]: https://github.com/Azure-Samples/app-service-java-manage-web-apps-with-custom-domains/
[4]: https://github.com/Azure-Samples/app-service-java-scale-web-apps-on-linux
[5]: https://github.com/Azure-Samples/app-service-java-manage-storage-connections-for-web-apps/
[6]: https://github.com/Azure-Samples/app-service-java-manage-data-connections-for-web-apps/