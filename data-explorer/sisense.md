---
title: Sisense를 사용하여 Azure 데이터 탐색기의 데이터 시각화
description: 이 문서에서는 Azure Data Explorer를 Sisense의 데이터 원본으로 설정하고 데이터를 시각화하는 방법을 알아봅니다.
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 5/29/2019
ms.openlocfilehash: 33a3e51e8bd892a5d4318445ff8e5f9ee26c4b40
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81501192"
---
# <a name="visualize-data-from-azure-data-explorer-in-sisense"></a>Sisense의 Azure 데이터 탐색기에서 데이터 시각화

Sisense는 대화형 사용자 경험을 제공하는 분석 앱을 빌드할 수 있는 분석 비즈니스 인텔리전스 플랫폼입니다. 비즈니스 인텔리전스 및 대시보드 보고 소프트웨어를 사용하면 몇 번의 클릭으로 데이터에 액세스하고 결합할 수 있습니다. 구조화되고 구조화되지 않은 데이터 원본에 연결하고, 최소한의 스크립팅 및 코딩으로 여러 소스의 테이블을 조인하고, 대화형 웹 대시보드 및 보고서를 만들 수 있습니다. 이 문서에서는 Azure Data Explorer를 Sisense의 데이터 원본으로 설정하고 샘플 클러스터에서 데이터를 시각화하는 방법을 알아봅니다.

## <a name="prerequisites"></a>사전 요구 사항

이 문서를 완료하려면 다음이 필요합니다.

* [다운로드 및 시센스 응용 프로그램을 설치](https://documentation.sisense.com/latest/getting-started/download-install.htm) 

* StormEvents 샘플 데이터가 포함된 클러스터 및 데이터베이스를 만듭니다. 자세한 내용은 [빠른 시작: Azure 데이터 탐색기 클러스터 및 데이터베이스 만들기 및](create-cluster-database-portal.md) Azure 데이터 [탐색기로 샘플 데이터 인제스트를](ingest-sample-data.md)참조하십시오.

    [!INCLUDE [data-explorer-storm-events](includes/data-explorer-storm-events.md)]

## <a name="connect-to-sisense-dashboards-using-azure-data-explorer-jdbc-connector"></a>Azure 데이터 탐색기 JDBC 커넥터를 사용하여 Sisense 대시보드에 연결

1. 다음 jar 파일의 최신 버전을 *다운로드하여 .에 복사합니다. \시센스\데이터 커넥터\jdbcdriver\adx* 

    * 활성화-1.1.jar
    * adal4j-1.6.0.jar
    * 커먼스 코덱-1.10.jar
    * 커먼스 컬렉션4-4.1.jar
    * 커먼스 랭3-3.5.jar
    * gson-2.8.0.jar
    * jcip 주석-1.0-1.jar
    * json-스마트-1.3.1.jar
    * 랭 태그-1.4.4.jar
    * 메일-1.4.7.jar
    * mssql-jdbc-7.2.1.jre8.jar
    * 님버스 호세 jwt-7.0.1.jar
    * oauth2-oidc-sdk-5.24.1.jar
    * slf4j-api-1.7.21.jar
    
1. 시센스 앱을 **엽니다.**
1. **데이터** 탭을 선택하고 **+ElastiCube를** 선택하여 새 엘라스티큐브 모델을 만듭니다.
    
    ![엘라스티큐브 선택](media/sisense/data-select-elasticube.png)

1. **새로운 엘라스티큐브 모델 추가에서 ElastiCube**모델의 이름을 지정하고 **저장합니다.**
   
    ![새로운 엘라스티큐브 모델 추가](media/sisense/add-new-elasticube-model.png)

1. **+ 데이터**선택 .

    ![데이터 버튼 선택](media/sisense/select-data.png)

1. **커넥터 선택** 탭에서 **일반 JDBC** 커넥터를 선택합니다.

    ![JDBC 커넥터 선택](media/sisense/select-connector.png)

1. **연결** 탭에서 **일반 JDBC** 커넥터의 다음 필드를 완료하고 **다음을**선택합니다.

    ![JDBC 커넥터 설정](media/sisense/jdbc-connector.png)

    |필드 |Description |
    |---------|---------|
    |연결 문자열     |   `jdbc:sqlserver://<cluster_name.region>.kusto.windows.net:1433;database=<database_name>;encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.kusto.windows.net;loginTimeout=30;authentication=ActiveDirectoryPassword`      |
    |JDBC JARs 폴더  |    `..\Sisense\DataConnectors\jdbcdrivers\adx`     |
    |드라이버 클래스 이름    |   `com.microsoft.sqlserver.jdbc.SQLServerDriver`      |
    |사용자 이름   |    AAD 사용자 이름     |
    |암호     |   AAD 사용자 암호      |

1. 데이터 **선택** 탭에서 **데이터베이스 선택을** 검색하여 사용 권한이 있는 관련 데이터베이스를 선택합니다. 이 예제에서는 *test1을*선택합니다.

    ![데이터베이스 선택](media/sisense/select-database.png)

1. *테스트(데이터베이스* 이름) 창:
    1. 테이블 이름을 선택하여 테이블을 미리 보고 테이블 열 이름을 확인합니다. 불필요한 열을 제거할 수 있습니다.
    1. 해당 테이블을 선택하려면 관련 테이블의 확인란을 선택합니다. 
    1. **완료** 를 선택합니다.

    ![테이블 선택](media/sisense/select-table-see-columns.png)    

1. **빌드를** 선택하여 데이터 집합을 빌드합니다. 

    * **빌드** 창에서 **빌드**를 선택합니다.

      ![빌드 창](media/sisense/build-window.png)

    * 빌드 프로세스가 완료될 때까지 기다린 다음 **성공한 빌드를 선택합니다.**

      ![빌드 성공](media/sisense/build-succeeded.png)

## <a name="create-sisense-dashboards"></a>Sisense 대시보드 만들기

1. **분석** 탭에서 **+**  >  **새 대시보드를** 선택하여 이 데이터 집합에서 대시보드를 만듭니다.

    ![새 대시보드](media/sisense/new-dashboard.png)

1. 대시보드를 선택하고 **에서 만들기를**선택합니다. 

    ![대시보드 만들기](media/sisense/create-dashboard.png)

1. **새 위젯에서** **+ 데이터 선택을 선택하여** 새 위젯을 만듭니다. 

    ![스톰이벤트 대시보드에 필드 추가](media/sisense/storm-dashboard-add-field.png)  

1. **+ 데이터 추가를** 선택하여 그래프에 열을 추가합니다. 

    ![그래프에 데이터 추가](media/sisense/add-more-data.png)

1. **+ 위젯을** 선택하여 다른 위젯을 만듭니다. 위젯을 드래그 앤 드롭하여 대시보드를 다시 정렬합니다.

    ![Storm 대시보드](media/sisense/final-dashboard.png)

이제 시각적 분석을 통해 데이터를 탐색하고, 추가 대시보드를 구축하고, 데이터를 실행 가능한 통찰력으로 변환하여 비즈니스에 영향을 미칠 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [Azure 데이터 탐색기용 쿼리 작성](write-queries.md)

