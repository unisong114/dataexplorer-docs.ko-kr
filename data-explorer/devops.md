---
title: Azure 데이터 탐색기용 Azure DevOps 작업
description: 이 항목에서는 릴리스 파이프라인을 만들고 배포하는 방법을 배웁니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: jasonh
ms.service: data-explorer
ms.topic: conceptual
ms.date: 05/05/2019
ms.openlocfilehash: ced839f671a94744799e8a6cde4dbee2a8f17cc3
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81498137"
---
# <a name="azure-devops-task-for-azure-data-explorer"></a>Azure 데이터 탐색기용 Azure DevOps 작업

[Azure DevOps 서비스는](https://azure.microsoft.com/services/devops/) 고성능 파이프라인, 무료 개인 Git 리포지토리, 구성 가능한 Kanban 보드 및 광범위한 자동화 및 연속 테스트 기능과 같은 개발 공동 작업 도구를 제공합니다. [Azure 파이프라인은](https://azure.microsoft.com/services/devops/pipelines/) CI/CD를 관리하여 모든 언어, 플랫폼 및 클라우드에서 작동하는 고성능 파이프라인을 사용하여 코드를 배포할 수 있는 Azure DevOps 기능입니다.
[Azure 데이터 탐색기 - 관리 명령은](https://marketplace.visualstudio.com/items?itemName=Azure-Kusto.PublishToADX) 릴리스 파이프라인을 만들고 Azure Data Explorer 데이터베이스에 데이터베이스 변경 내용을 배포할 수 있는 Azure 파이프라인 작업입니다. [비주얼 스튜디오 마켓플레이스에서](https://marketplace.visualstudio.com/)무료로 이용할 수 있다.

이 문서에서는 **Azure Data Explorer - 관리 명령** 작업을 사용하여 스키마 변경 내용을 데이터베이스에 배포하는 방법에 대한 간단한 예제를 설명합니다. 전체 CI/CD 파이프라인은 [Azure DevOps 설명서를](/azure/devops/user-guide/what-is-azure-devops?view=azure-devops#vsts)참조하십시오.

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독이 아직 없는 경우 시작하기 전에 [Azure 체험 계정](https://azure.microsoft.com/free/)을 만듭니다.
* Azure 데이터 탐색기 클러스터 설정:
    * [Azure 데이터 탐색기 클러스터 및 데이터베이스](/azure/data-explorer/create-cluster-database-portal)
    * Azure AD 응용 프로그램을 프로비전하여 Azure Active Directory(Azure [AD) 앱을](kusto/management/access-control/how-to-provision-aad-app.md)만듭니다.
    * Azure Data Explorer 데이터베이스 권한을 관리하여 Azure Data Explorer 데이터베이스의 Azure AD 앱에 대한 액세스 권한을 [부여합니다.](/azure/data-explorer/manage-database-permissions)
* Azure DevOps 설정:
    * [무료 조직에 가입](/azure/devops/user-guide/sign-up-invite-teammates?view=azure-devops)
    * [조직 만들기](/azure/devops/organizations/accounts/create-organization?view=azure-devops)
    * [Azure DevOps에서 프로젝트 만들기](/azure/devops/organizations/projects/create-project?view=azure-devops)
    * [Git코드가 있는 코드](/azure/devops/user-guide/code-with-git?view=azure-devops)

## <a name="create-folders"></a>폴더 만들기

Git 리포지토리에 다음 샘플*폴더(함수,* *정책,* *표)를*만듭니다. [아래와](https://github.com/Azure/azure-kusto-docs-samples/tree/master/DevOps_release_pipeline) 같이 여기에서 파일을 각 폴더에 복사하고 변경 내용을 커밋합니다. 샘플 파일은 다음 워크플로를 실행하기 위해 제공됩니다.

![폴더 만들기](media/devops/create-folders.png)

> [!TIP]
> 사용자 고유의 워크플로를 만들 때 코드를 idempotent로 만드는 것이 좋습니다. 예를 들어 [.create 테이블 대신 .create-merge 테이블을](kusto/management/create-table-command.md#create-merge-table) 사용하고 .create 함수 대신 [.create-or-alter](kusto/management/create-alter-function.md) [함수를](kusto/management/create-function.md) 사용합니다. [.create table](kusto/management/create-table-command.md)

## <a name="create-a-release-pipeline"></a>릴리스 파이프라인 만들기

1. [Azure DevOps 조직에 로그인합니다.](https://dev.azure.com/)
1. 왼쪽 메뉴에서 **파이프라인 릴리스를** > **Releases** 선택하고 **새 파이프라인을**선택합니다.

    ![새 파이프라인](media/devops/new-pipeline.png)

1. **새 릴리스 파이프라인** 창이 열립니다. **파이프라인** 탭에서 **템플릿 선택** 창에서 빈 **작업을**선택합니다.

     ![템플릿 선택](media/devops/select-template.png)

1. **스테이지** 버튼을 선택합니다. **스테이지** 창에서 스테이지 **이름을**추가합니다. 파이프라인을 저장하려면 **저장을** 선택합니다.

    ![스테이지 이름 지정](media/devops/stage-name.png)

1. **아티팩트 추가 단추를** 선택합니다. **아티팩트 추가** 창에서 코드가 있는 리포지토리를 선택하고 관련 정보를 입력한 다음 **추가를**클릭합니다. 파이프라인을 저장하려면 **저장을** 선택합니다.

    ![아티팩트 추가](media/devops/add-artifact.png)

1. **변수** 탭에서 + **추가를** 선택하여 작업에 사용할 **끝점 URL에** 대한 변수를 만듭니다. 끝점의 **이름과** **값을** 작성합니다. 파이프라인을 저장하려면 **저장을** 선택합니다. 

    ![변수 만들기](media/devops/create-variable.png)

    Endpoint_URL 찾으려면 Azure 포털의 **Azure 데이터 탐색기 클러스터의** 개요 페이지에 Azure 데이터 탐색기 클러스터 URI가 포함되어 있습니다. URI를 다음 형식으로 `https://<Azure Data Explorer cluster URI>?DatabaseName=<DBName>`구성합니다.  예를 들어 https:\//kustodocs.westus.kusto.windows.net?데이터베이스이름=샘플DB

    ![Azure 데이터 탐색기 클러스터 URI](media/devops/adx-cluster-uri.png)

## <a name="create-tasks-to-deploy"></a>배포할 작업 만들기

1. **파이프라인** 탭에서 작업 **1개, 작업 0을** 클릭하여 작업을 추가합니다. 

    ![작업 추가](media/devops/add-task.png)

1. **테이블,** **함수**및 **정책**순서대로 배포하는 세 가지 작업을 만듭니다. 

1. **작업** 탭에서 에이전트 **+** **작업별로**선택합니다. **Azure 데이터 탐색기**를 검색합니다. **마켓플레이스에서**Azure 데이터 탐색기 - 관리자 명령 확장을 **설치합니다.** 그런 다음 **Azure 데이터 탐색기 실행 명령에서 추가를 선택합니다.** **Add**

     ![관리자 명령 추가](media/devops/add-admin-commands.png)

1. 왼쪽에 있는 **Kusto 명령을** 클릭하고 다음 정보로 작업을 업데이트합니다.
    * **이름 표시**: 작업 이름
    * **파일 경로**: **테이블** 작업에서 테이블 생성 파일이 *테이블* 폴더에 있기 때문에 */테이블/*.csl을 지정합니다.
    * **끝점 URL**: `EndPoint URL`이전 단계에서 만든 변수를 입력합니다.
    * **서비스 끝점 사용을** 선택하고 + **새**을 선택합니다.

    ![쿠스토 명령 작업 업데이트](media/devops/kusto-command-task.png)

1. **Azure 데이터 탐색기 서비스 연결 연결 창에서** 다음 정보를 완료합니다.

    |설정  |제안 값  |
    |---------|---------|
    |**연결 이름**     |    이 서비스 끝점을 식별하기 위해 이름을 입력합니다.     |
    |**클러스터 URL**    |    Azure 포털의 Azure 데이터 탐색기 클러스터의 개요 섹션에서 값을 찾을 수 있습니다. | 
    |**서비스 주체 ID**    |    AAD 앱 ID(필수 구성 조건으로 생성됨)를 입력합니다.     |
    |**서비스 주체 앱 키**     |    AAD 앱 키(필수 구성 조건으로 생성됨)를 입력합니다.    |
    |**AAD 테넌트 ID**    |      AAD 테넌트(예: microsoft.com, contoso.com...)    |

    **모든 파이프라인이 이 연결 확인란을 사용하도록 허용을 선택합니다.** **확인**을 선택합니다.

    ![서비스 연결 추가](media/devops/add-service-connection.png)

1. 1-5단계를 다시 두 번 반복하여 함수 및 *정책* 폴더에서 파일을 *배포합니다.* **저장**을 선택합니다. **작업** 탭에서 생성된 세 가지 **작업(테이블 배포,** 함수 배포 및 **정책 배포)을**참조하십시오. **Deploy Functions**

    ![모든 폴더 배포](media/devops/deploy-all-folders.png)

1. **+ 릴리스** > 만들기를 선택하여**릴리스를** 만듭니다.

    ![릴리스 만들기](media/devops/create-release.png)

1. **로그** 탭에서 배포 상태가 성공여부를 확인합니다.

    ![배포성공](media/devops/deployment-successful.png)

이제 세 가지 작업을 사전 프로덕션에 배포하기 위한 릴리스 파이프라인 만들기를 완료했습니다.