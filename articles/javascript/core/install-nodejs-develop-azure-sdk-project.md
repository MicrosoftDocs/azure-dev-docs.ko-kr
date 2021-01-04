---
title: Azure SDK 애플리케이션 개발용 Node.js 설치
description: Azure를 사용한 일반적인 개발 시나리오를 위해 Node.js를 설치하는 방법을 알아봅니다.
ms.topic: how-to
ms.date: 12/04/2020
ms.custom: devx-track-js
ms.openlocfilehash: 5fdaca001f82afca942a22ec9ad971f4af18c8b8
ms.sourcegitcommit: 525c4b41d85aae9c3026a070b07e00c2241ea716
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/14/2020
ms.locfileid: "97394041"
---
# <a name="install-and-manage-nodejs-for-common-azure-development-scenarios"></a>일반적인 Azure 개발 시나리오를 위해 Node.js 설치 및 관리

Azure 개발용 Node.js를 설치할 때는 로컬 개발 환경과 배포 대상 호스팅 환경을 모두 고려해야 합니다. Azure는 LTS(장기 지원) 버전의 Windows 및 Linux에서 Node.js 호스팅을 제공합니다. 

## <a name="minimum-version-of-nodejs-8"></a>최소 버전: Node.js 8+

Azure SDK가 지원하는 최소 버전은 Node.js 8입니다. 

## <a name="download-and-install-nodejs-based-on-your-intended-use"></a>원하는 용도에 따라 Node.js 다운로드 및 설치

요구 사항에 따라 Node.js를 다운로드하고 설치할 수 있습니다.
 
* [Node.js 다운로드 페이지](https://nodejs.org/en/download/) 
* [공식 Docker 이미지](https://hub.docker.com/_/node/)

## <a name="managing-versions-of-nodejs"></a>Node.js의 버전 관리

Azure 개발을 위해 여러 버전의 Node.js를 관리해야 하는 경우 nvm을 사용합니다.

* OSX, *nix - [nvm](https://github.com/creationix/nvm)
* Windows - [Windows용 nvm](https://github.com/marcelklehr/nodist)

## <a name="next-steps"></a>다음 단계

* Azure SDK 사용을 위한 [로컬 개발 환경 구성](configure-local-development-environment.md)
