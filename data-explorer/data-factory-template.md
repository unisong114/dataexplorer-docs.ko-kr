---
title: Azure 데이터 팩터리 템플릿을 사용하여 데이터베이스에서 Azure 데이터 탐색기로 대량 복사
description: 이 문서에서는 Azure Data Factory 템플릿을 사용하여 데이터베이스에서 Azure 데이터 탐색기로 대량으로 복사하는 방법을 배웁니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/08/2019
ms.openlocfilehash: 884f4e956b37c2def6c25d0acdf20f15eddf7767
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81498241"
---
# <a name="copy-in-bulk-from-a-database-to-azure-data-explorer-by-using-the-azure-data-factory-template"></a>Azure 데이터 팩터리 템플릿을 사용하여 데이터베이스에서 Azure 데이터 탐색기로 대량 복사 

Azure 데이터 탐색기는 빠르고 완벽하게 관리되는 데이터 분석 서비스입니다. 응용 프로그램, 웹 사이트 및 IoT 장치와 같은 많은 소스에서 스트리밍되는 대용량 데이터에 대한 실시간 분석을 제공합니다. 

오라클 서버, 네테자, 테라데이타 또는 SQL Server의 데이터베이스에서 Azure 데이터 탐색기로 데이터를 복사하려면 여러 테이블에서 엄청난 양의 데이터를 로드해야 합니다. 일반적으로 단일 테이블에서 여러 스레드가 있는 행을 로드할 수 있도록 각 테이블에서 데이터를 분할해야 합니다. 이 문서에서는 이러한 시나리오에서 사용할 템플릿에 대해 설명합니다.

[Azure 데이터 팩터리 템플릿은](/azure/data-factory/solution-templates-introduction) 미리 정의된 데이터 팩터리 파이프라인입니다. 이러한 템플릿을 사용하면 데이터 팩터리를 빠르게 시작하고 데이터 통합 프로젝트의 개발 시간을 줄일 수 있습니다. 

*조회* 및 *ForEach* 활동을 사용하여 *데이터베이스에서 Azure 데이터 탐색기* 템플릿으로 대량 복사본을 만듭니다. 더 빠른 데이터 복사를 위해 템플릿을 사용하여 데이터베이스 또는 테이블당 많은 파이프라인을 만들 수 있습니다. 

> [!IMPORTANT]
> 복사하려는 데이터 양에 적합한 도구를 사용해야 합니다.
> * *데이터베이스에서 Azure 데이터 탐색기 템플릿으로 대량 복사를* 사용하여 SQL 서버 및 Google BigQuery와 같은 데이터베이스에서 Azure 데이터 탐색기로 많은 양의 데이터를 복사합니다. 
> * 데이터 [*팩터리 복사 데이터 도구를*](data-factory-load-data.md) 사용하여 소량 또는 중간 정도의 데이터가 있는 몇 개의 테이블을 Azure 데이터 탐색기로 복사합니다. 

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독이 아직 없는 경우 시작하기 전에 [Azure 체험 계정](https://azure.microsoft.com/free/)을 만듭니다.
* [Azure Data Explorer 클러스터 및 데이터베이스](create-cluster-database-portal.md).
* [데이터 팩터리 만들기.](data-factory-load-data.md#create-a-data-factory)
* 데이터베이스의 데이터 원본입니다.

## <a name="create-controltabledataset"></a>제어 테이블데이터 집합 만들기

*ControlTableDataset은* 원본에서 파이프라인의 대상으로 복사할 데이터를 나타냅니다. 행 수는 데이터를 복사하는 데 필요한 파이프라인의 총 수를 나타냅니다. 컨트롤테이블데이터집합을 소스 데이터베이스의 일부로 정의해야 합니다.

SQL Server 소스 테이블 형식의 예는 다음 코드에 나와 있습니다.
    
```sql   
CREATE TABLE control_table (
PartitionId int,
SourceQuery varchar(255),
ADXTableName varchar(255)
);
```

코드 요소는 다음 표에 설명되어 있습니다.

|속성  |Description  | 예제
|---------|---------| ---------|
|PartitionId   |  복사 순서 | 1  |  
|소스 쿼리   |  파이프라인 런타임 중에 복사할 데이터를 나타내는 쿼리 | <br>`select * from table where lastmodifiedtime  LastModifytime >= ''2015-01-01 00:00:00''>` </br>    
|ADX테이블 이름  |  대상 테이블 이름 | 마이아덱스 테이블       |  

컨트롤테이블데이터집합이 다른 형식인 경우 형식에 대해 비슷한 ControlTableDataset을 만듭니다.

## <a name="use-the-bulk-copy-from-database-to-azure-data-explorer-template"></a>데이터베이스에서 Azure 데이터 탐색기 템플릿으로 대량 복사 사용

1. 시작 **창에서** **템플릿에서 파이프라인 만들기를** 선택하여 템플릿 **갤러리** 창을 엽니다.

    ![Azure 데이터 팩터리 "시작해 보겠습니다" 창](media/data-factory-template/adf-get-started.png)

1. **데이터베이스에서 Azure 데이터 탐색기 템플릿으로 대량 복사를** 선택합니다.
 
    !["데이터베이스에서 Azure 데이터 탐색기로의 대량 복사" 템플릿](media/data-factory-template/pipeline-from-template.png)

1.  **데이터베이스에서 Azure 데이터 탐색기 창에** 대한 대량 복사에서 **사용자 입력에서**다음을 수행하여 데이터 집합을 지정합니다. 

    a. **ControlTableDataset** 드롭다운 목록에서 원본에서 대상으로 복사되는 데이터와 대상에 배치할 위치를 나타내는 제어 테이블에 연결된 서비스를 선택합니다. 

    b. **SourceDataset** 드롭다운 목록에서 원본 데이터베이스에 연결된 서비스를 선택합니다. 

    다. **AzureDataExplorerTable** 드롭다운 목록에서 Azure 데이터 탐색기 테이블을 선택합니다. 데이터 집합이 없는 경우 [Azure Data Explorer 링크된 서비스를 만들어](data-factory-load-data.md#create-the-azure-data-explorer-linked-service) 데이터 집합을 추가합니다.

    d. **이 템플릿 사용을**선택합니다.

    !["데이터베이스에서 Azure 데이터 탐색기로의 대량 복사" 창](media/data-factory-template/configure-bulk-copy-adx-template.png)

1. 캔버스에서 활동 외부의 영역을 선택하여 템플릿 파이프라인에 액세스합니다. 매개 **변수 탭을** 선택하여 **이름(제어** 테이블 이름) 및 **기본값(열** 이름)을 포함하여 테이블의 매개 변수를 입력합니다.

    ![파이프라인 매개 변수](media/data-factory-template/pipeline-parameters.png)

1.  **조회에서** **GetPartitionList를** 선택하여 기본 설정을 봅니다. 쿼리가 자동으로 만들어집니다.
1.  명령 활동을 선택, **ForEachPartition**, **설정** 탭을 선택한 다음 다음을 수행합니다.

    a. 일괄 **처리 개수** 상자에 숫자를 1에서 50까지 입력합니다. 이 선택은 *ControlTableDataset* 행의 수에 도달할 때까지 병렬로 실행되는 파이프라인 수를 결정합니다. 

    b. 파이프라인 일괄 처리가 병렬로 실행되도록 하려면 **순차** 확인란을 *선택하지 마십시오.*

    ![ForEach파티션 설정](media/data-factory-template/foreach-partition-settings.png)

    > [!TIP]
    > 가장 좋은 방법은 데이터를 더 빠르게 복사할 수 있도록 많은 파이프라인을 병렬로 실행하는 것입니다. 효율성을 높이기 위해 원본 테이블의 데이터를 분할하고 날짜 및 테이블에 따라 파이프라인당 하나의 파티션을 할당합니다.

1. **모두 유효성 검사를** 선택하여 Azure 데이터 팩터리 파이프라인의 유효성을 검사한 다음 파이프라인 유효성 검사 **출력** 창에서 결과를 봅니다.

    ![템플릿 파이프라인 유효성 검사](media/data-factory-template/validate-template-pipelines.png)

1. 필요한 경우 **디버그**를 선택한 다음 **트리거 추가를** 선택하여 파이프라인을 실행합니다.

    !["디버그" 및 "파이프라인 실행" 단추](media/data-factory-template/trigger-run-of-pipeline.png)    

이제 템플릿을 사용하여 데이터베이스 및 테이블에서 많은 양의 데이터를 효율적으로 복사할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [Azure 데이터 팩터리 를 사용하여 Azure 데이터 탐색기로 데이터를 복사하는](data-factory-load-data.md)방법에 대해 알아봅니다.
* Azure 데이터 팩터리의 [Azure 데이터 탐색기 커넥터에](/azure/data-factory/connector-azure-data-explorer) 대해 알아봅니다.
* 데이터 [쿼리에 대한 Azure 데이터 탐색기 쿼리에](/azure/data-explorer/web-query-data) 대해 자세히 알아보세요.






