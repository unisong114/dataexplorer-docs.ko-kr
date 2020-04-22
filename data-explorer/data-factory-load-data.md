---
title: Azure 데이터 팩터리에서 Azure 데이터 탐색기로 데이터 복사
description: 이 문서에서는 Azure Data Factory 복사 도구를 사용하여 Azure 데이터 탐색기로 데이터를 수집(로드)하는 방법을 알아봅니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: jasonh
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/15/2019
ms.openlocfilehash: c937d69df73ed523d1e4f3f0614eadf249ced0f3
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81498007"
---
# <a name="copy-data-to-azure-data-explorer-by-using-azure-data-factory"></a>Azure 데이터 팩터리를 사용하여 데이터를 Azure 데이터 탐색기로 복사합니다. 

Azure 데이터 탐색기는 빠르고 완벽하게 관리되는 데이터 분석 서비스입니다. 응용 프로그램, 웹 사이트 및 IoT 장치와 같은 많은 소스에서 스트리밍되는 대용량 데이터에 대한 실시간 분석을 제공합니다. Azure Data Explorer를 사용하면 데이터를 반복적으로 탐색하고 패턴 및 이상 징후를 식별하여 제품을 개선하고, 고객 경험을 개선하고, 장치를 모니터링하고, 작업을 향상시킬 수 있습니다. 새로운 질문을 탐색하고 몇 분 안에 답변을 얻을 수 있습니다. 

Azure Data Factory는 완전히 관리되는 클라우드 기반 데이터 통합 서비스입니다. 이를 사용하여 Azure Data Explorer 데이터베이스를 기존 시스템의 데이터로 채울 수 있습니다. 분석 솔루션을 구축할 때 시간을 절약할 수 있습니다.

Azure 데이터 탐색기로 데이터를 로드할 때 데이터 팩터리는 다음과 같은 이점을 제공합니다.

* **간편한 설정**: 스크립팅이 필요 없는 직관적인 5단계 마법사를 가져옵니다.
* **리치 데이터 저장소 지원**: 풍부한 온-프레미스 및 클라우드 기반 데이터 저장소 집합에 대한 기본 제공 지원을 받으십시오. 자세한 목록은 [지원되는 데이터 저장소](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats) 표를 참조하세요.
* **보안 및 준수**: 데이터가 HTTPS 또는 Azure ExpressRoute를 통해 전송됩니다. 글로벌 서비스가 제공되므로 데이터가 지리적 경계를 벗어나지 않습니다.
* **고성능**: Azure 데이터 탐색기로 데이터 로드 속도는 초당 최대 1GB(GBps)입니다. 자세한 내용은 [활동 복사 성능](/azure/data-factory/copy-activity-performance)을 참조하십시오.

이 문서에서는 데이터 팩터리 복사 데이터 도구를 사용하여 Amazon 단순 저장소 서비스(S3)의 데이터를 Azure 데이터 탐색기로 로드합니다. 다음과 같은 유사한 프로세스를 따라 다른 데이터 저장소의 데이터를 복사할 수 있습니다.
* [Azure Blob 저장소](/azure/data-factory/connector-azure-blob-storage)
* [Azure SQL Database](/azure/data-factory/connector-azure-sql-database)
* [Azure SQL Data Warehouse](/azure/data-factory/connector-azure-sql-data-warehouse)
* [Google BigQuery](/azure/data-factory/connector-google-bigquery)
* [Oracle](/azure/data-factory/connector-oracle)
* [파일 시스템](/azure/data-factory/connector-file-system)

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독이 아직 없는 경우 시작하기 전에 [Azure 체험 계정](https://azure.microsoft.com/free/)을 만듭니다.
* [Azure Data Explorer 클러스터 및 데이터베이스](create-cluster-database-portal.md).
* 데이터 원본입니다.

## <a name="create-a-data-factory"></a>데이터 팩터리 만들기

1. [Azure Portal](https://ms.portal.azure.com)에 로그인합니다.

1. 왼쪽 창에서 > 리소스**분석** > **데이터 팩터리** **만들기를**선택합니다.

   ![Azure 포털에서 데이터 팩터리 만들기](media/data-factory-load-data/create-adf.png)

1. 새 **데이터 팩터리** 창에서 다음 표의 필드에 대한 값을 제공합니다.

   !["새 데이터 팩터리" 창](media/data-factory-load-data/my-new-data-factory.png)  

   | 설정  | 입력할 값  |
   |---|---|
   | **이름** | 상자에 데이터 팩터리의 전역 고유 이름을 입력합니다. 오류가 발생하면 데이터 *팩터리 \"이름\" LoadADXDemo을 사용할 수 없으며*데이터 팩터리의 다른 이름을 입력합니다. 데이터 팩터리 아티팩트 의 이름 지정에 대한 규칙은 [데이터 팩터리 이름 지정 규칙을](/azure/data-factory/naming-rules)참조하십시오.|
   | **구독** | 드롭다운 목록에서 데이터 팩터리를 만들 Azure 구독을 선택합니다. |
   | **리소스 그룹** | 새 을 **선택한**다음 새 리소스 그룹의 이름을 입력합니다. 리소스 그룹이 이미 있는 경우 **기존 을 선택합니다.** |
   | **Version** | 드롭다운 목록에서 **V2**를 선택합니다. |    
   | **위치** | 드롭다운 목록에서 데이터 팩터리의 위치를 선택합니다. 지원되는 위치만 목록에 표시됩니다. 데이터 팩터리에서 사용하는 데이터 저장소는 다른 위치 나 지역에 존재할 수 있습니다. |

1. **만들기**를 선택합니다.

1. 생성 프로세스를 모니터링하려면 도구 모음에서 **알림을** 선택합니다. 데이터 팩터리를 만든 후 선택합니다.
   
   **데이터 팩터리** 창이 열립니다.

   ![데이터 팩터리 창](media/data-factory-load-data/data-factory-home-page.png)

1. 별도의 창에서 응용 프로그램을 열려면 **모니터 작성** & 선택합니다.

## <a name="load-data-into-azure-data-explorer"></a>Azure 데이터 탐색기로 데이터 로드

여러 유형의 [데이터 저장소의](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats) 데이터를 Azure 데이터 탐색기로 로드할 수 있습니다. 이 문서에서는 Amazon S3에서 데이터를 로드하는 방법에 대해 설명합니다.

다음 방법 중 하나에서 데이터를 로드할 수 있습니다.

* Azure 데이터 팩터리 사용자 인터페이스에서 왼쪽 창에서 **작성자** 아이콘을 선택합니다. Azure [데이터 팩터리 UI를 사용하여 데이터 팩터리 만들기의](/azure/data-factory/quickstart-create-data-factory-portal#create-a-data-factory)"데이터 팩터리 만들기" 섹션에 표시됩니다.
* 에 표시된 대로 Azure 데이터 팩터리 복사 데이터 도구에서 [데이터 복사 도구를 사용하여 데이터를 복사합니다.](/azure/data-factory/quickstart-create-data-factory-copy-data-tool)

### <a name="copy-data-from-amazon-s3-source"></a>아마존 S3에서 데이터 복사 (소스)

1. 시작 **창에서** **데이터**복사 를 선택하여 데이터 복사 도구를 엽니다.

   ![데이터 복사 도구 단추](media/data-factory-load-data/copy-data-tool-tile.png)

1. **속성** 창에서 **작업 이름** 상자에 이름을 입력한 다음 **다음을**선택합니다.

    ![데이터 속성 복사 창](media/data-factory-load-data/copy-from-source.png)

1. 원본 **데이터 저장소** 창에서 **새 연결 만들기를**선택합니다.

    ![복사 데이터 "원본 데이터 저장소" 창](media/data-factory-load-data/source-create-connection.png)

1. **Amazon S3을**선택한 다음 **계속을**선택합니다.

    ![새로운 연결된 서비스 창](media/data-factory-load-data/amazons3-select-new-linked-service.png)

1. 새 **연결 서비스(Amazon S3)** 창에서 다음을 수행합니다.

    ![아마존 S3 연결 서비스 지정](media/data-factory-load-data/amazons3-new-linked-service-properties.png)

    a. **이름** 상자에 새 연결된 서비스의 이름을 입력합니다.

    b. 통합 **런타임** 드롭다운 목록을 통해 연결에서 값을 선택합니다.

    다. 액세스 **키 ID** 상자에 값을 입력합니다.
    
    > [!NOTE]
    > Amazon S3에서 액세스 키를 찾으려면 탐색 모음에서 Amazon 사용자 이름을 선택한 다음 **내 보안 자격 증명을 선택합니다.**
    
    d. 비밀 **액세스 키** 상자에 값을 입력합니다.

    e. 만든 연결된 서비스 연결을 테스트하려면 **연결 테스트**를 선택합니다.

    f. **마침**을 선택합니다.
    
      **소스 데이터 저장소** 창에 새 AmazonS31 연결이 표시됩니다. 

1. **다음**을 선택합니다.

   ![원본 데이터 저장소 생성된 연결](media/data-factory-load-data/source-data-store-created-connection.png)

1. 입력 **파일 또는 폴더** 창 선택에서 다음 단계를 수행합니다.

    a. 복사할 파일 이나 폴더를 찾아 본 다음 선택 합니다.

    b. 원하는 복사 동작을 선택합니다. **이진 복사** 확인란이 지워졌는지 확인합니다.

    다. **다음**을 선택합니다.

    ![입력 파일 또는 폴더 선택](media/data-factory-load-data/source-choose-input-file.png)

1. 파일 **형식 설정** 창에서 파일의 관련 설정을 선택합니다. 다음을 **Next**선택합니다.

   !["파일 형식 설정" 창](media/data-factory-load-data/source-file-format-settings.png)

### <a name="copy-data-into-azure-data-explorer-destination"></a>Azure 데이터 탐색기(대상)로 데이터 복사

새 Azure Data 탐색기 링크 된 서비스는 이 섹션에 지정된 Azure Data 탐색기 대상 테이블 (싱크)에 데이터를 복사하기 위해 만들어집니다.

> [!NOTE]
> Azure [Data Factory 명령 활동을](data-factory-command-activity.md) 사용하여 Azure Data Explorer 컨트롤 명령을 실행하고 와 같은 `.set-or-replace` [쿼리 명령에서 수집을](kusto/management/data-ingestion/ingest-from-query.md)사용합니다.

#### <a name="create-the-azure-data-explorer-linked-service"></a>Azure 데이터 탐색기 연결 서비스 만들기

Azure 데이터 탐색기 연결된 서비스를 만들려면 다음 단계를 수행합니다.

1. 기존 데이터 저장소 연결을 사용하거나 새 데이터 저장소를 지정하려면 **대상 데이터 저장소** 창에서 새 연결 **만들기를**선택합니다.

    ![대상 데이터 저장소 창](media/data-factory-load-data/destination-create-connection.png)

1. 새 **연결된 서비스** 창에서 **Azure 데이터 탐색기를**선택한 다음 **계속을**선택합니다.

    ![새 연결 서비스 창](media/data-factory-load-data/adx-select-new-linked-service.png)

1. 새 **연결된 서비스(Azure 데이터 탐색기)** 창에서 다음 단계를 수행합니다.

    ![Azure 데이터 탐색기 새 연결 된 서비스 창](media/data-factory-load-data/adx-new-linked-service.png)

   a. **이름** 상자에 Azure 데이터 탐색기 링크된 서비스의 이름을 입력합니다.

   b. **계정 선택 방법에서**다음 옵션 중 하나를 선택합니다. 

    * **Azure 구독에서** 선택한 다음 드롭다운 목록에서 Azure **구독** 및 **클러스터를**선택합니다. 

        > [!NOTE]
        > **클러스터** 드롭다운 컨트롤은 구독과 연결된 클러스터만 나열합니다.

    * **수동으로 입력을**선택한 다음 **끝점을**입력합니다.

   다. **테넌트 상자에 테넌트** 이름을 입력합니다.

   d. 서비스 **주체 ID** 상자에 서비스 주체 ID를 입력합니다.

   e. **서비스 주체 키를** 선택한 다음 **서비스 주체 키** 상자에 키 값을 입력합니다.

   f. **데이터베이스** 드롭다운 목록에서 데이터베이스 이름을 선택합니다. 또는 **편집** 확인란을 선택한 다음 데이터베이스 이름을 입력합니다.

   g. 만든 연결된 서비스 연결을 테스트하려면 **연결 테스트**를 선택합니다. 연결된 서비스에 연결할 수 있는 경우 창에 녹색 체크 표시와 **연결 성공** 메시지가 표시됩니다.

   h. 연결된 서비스 생성을 완료하려면 **완료를** 선택합니다.

    > [!NOTE]
    > 서비스 주체는 Azure 데이터 팩터리에서 Azure 데이터 탐색기 서비스에 액세스하는 데 사용됩니다. 서비스 주체를 만들려면 [Azure Active Directory(Azure AD) 서비스 보안 주체를 만듭니다.](/azure-stack/operator/azure-stack-create-service-principals#manage-an-azure-ad-service-principal) Azure 키 볼트 방법을 사용하지 마십시오.

#### <a name="configure-the-azure-data-explorer-data-connection"></a>Azure 데이터 탐색기 데이터 연결 구성

연결된 서비스 연결을 만든 후 **대상 데이터 저장소** 창이 열리고 만든 연결을 사용할 수 있습니다. 연결을 구성하려면 다음 단계를 수행합니다.

1. **다음**을 선택합니다.

    ![Azure 데이터 탐색기 "대상 데이터 저장소" 창](media/data-factory-load-data/destination-data-store.png)

1. 테이블 **매핑** 창에서 대상 테이블 이름을 설정한 다음 **다음을**선택합니다.

    ![대상 데이터 집합 "테이블 매핑" 창](media/data-factory-load-data/destination-dataset-table-mapping.png)

1. 열 **매핑** 창에서 다음과 같은 매핑이 수행됩니다.

    a. 첫 번째 매핑은 Azure 데이터 팩터리 [스키마 매핑에 따라 Azure 데이터 팩터리에서](/azure/data-factory/copy-activity-schema-and-type-mapping)수행됩니다. 다음을 수행합니다.

    * Azure 데이터 팩터리 대상 테이블에 대한 **열 매핑을 설정합니다.** 기본 매핑은 원본에서 Azure Data Factory 대상 테이블로 표시됩니다.

    * 열 매핑을 정의할 필요가 없는 열 선택을 취소합니다.

    b. 두 번째 매핑은 이 테이블 형식 데이터가 Azure 데이터 탐색기로 수집될 때 발생합니다. 매핑은 [CSV 매핑 규칙에](kusto/management/mappings.md#csv-mapping)따라 수행됩니다. 원본 데이터가 CSV 형식이 아니더라도 Azure Data Factory는 데이터를 테이블 형식으로 변환합니다. 따라서 CSV 매핑은 이 단계에서 유일한 관련 매핑입니다. 다음을 수행합니다.

    * (선택 사항) **Kusto(Azure Data Explorer) 싱크 속성**에서 열 매핑을 사용할 수 있도록 관련 **인기 변환 매핑 이름을** 추가합니다.

    * **인베이션 매핑 이름을** 지정하지 않으면 열 매핑 섹션에 정의된 *이름 별* **매핑 순서가** 사용됩니다. *이름별* 매핑에 실패하면 Azure Data Explorer는 데이터를 *열별 위치* 순서로 수집하려고 시도합니다(즉, 위치별로 기본값으로 매핑).

    * **다음**을 선택합니다.

    ![대상 데이터 집합 "열 매핑" 창](media/data-factory-load-data/destination-dataset-column-mapping.png)

1. **설정** 창에서 다음 단계를 수행합니다.

    a. **내결함성 설정에서**관련 설정을 입력합니다.

    b. **성능 설정에서**준비 **활성화가** 적용되지 않으며 **고급 설정에는** 비용 고려 사항이 포함됩니다. 특정 요구 사항이 없는 경우 이러한 설정을 그대로 둡니다.

    다. **다음**을 선택합니다.

    ![복사 데이터 "설정" 창](media/data-factory-load-data/copy-data-settings.png)

1. **요약** 창에서 설정을 검토한 다음 **다음을**선택합니다.

    ![복사 데이터 "요약" 창](media/data-factory-load-data/copy-data-summary.png)

1. 배포 **완료** 창에서 다음을 수행합니다.

    a. **모니터** 탭으로 전환하고 파이프라인상태(즉, 진행률, 오류 및 데이터 흐름)를 보려면 **모니터**를 선택합니다.

    b. 연결된 서비스, 데이터 집합 및 파이프라인을 편집하려면 **파이프라인 편집을**선택합니다.

    다. 복사 데이터 작업을 완료하려면 **완료를** 선택합니다.

    !["배포 완료" 창](media/data-factory-load-data/deployment.png)

## <a name="next-steps"></a>다음 단계

* Azure 데이터 팩터리의 [Azure 데이터 탐색기 커넥터에](/azure/data-factory/connector-azure-data-explorer) 대해 알아봅니다.
* [데이터 팩터리 UI에서](/azure/data-factory/quickstart-create-data-factory-portal)연결된 서비스, 데이터 집합 및 파이프라인 편집에 대해 자세히 알아봅니다.
* 데이터 [쿼리에 대한 Azure 데이터 탐색기 쿼리에](/azure/data-explorer/web-query-data) 대해 자세히 알아보세요.
