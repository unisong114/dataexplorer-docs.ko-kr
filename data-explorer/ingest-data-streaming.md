---
title: Azure Portal를 사용 하 여 Azure 데이터 탐색기 클러스터에서 스트리밍 수집 구성
description: Azure Portal를 사용 하 여 Azure 데이터 탐색기 클러스터를 구성 하 고 스트리밍 수집을 사용 하 여 데이터 로드를 시작 하는 방법을 알아봅니다.
author: orspod
ms.author: orspodek
ms.reviewer: alexefro
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/13/2020
ms.openlocfilehash: 7e5d836e25916a039a4df8e451d16e4f8a41cf18
ms.sourcegitcommit: 537a7eaf8c8e06a5bde57503fedd1c3706dd2b45
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/16/2020
ms.locfileid: "86423174"
---
# <a name="configure-streaming-ingestion-on-your-azure-data-explorer-cluster-using-the-azure-portal"></a>Azure Portal를 사용 하 여 Azure 데이터 탐색기 클러스터에서 스트리밍 수집 구성

> [!div class="op_single_selector"]
> * [포털](ingest-data-streaming.md)
> * [C#](ingest-data-streaming-csharp.md)

[!INCLUDE [ingest-data-streaming-intro](includes/ingest-data-streaming-intro.md)]

## <a name="prerequisites"></a>필수 구성 요소

* Azure 구독이 아직 없는 경우 시작하기 전에 [Azure 체험 계정](https://azure.microsoft.com/free/)을 만듭니다.
* [Azure 데이터 탐색기 클러스터 및 데이터베이스](create-cluster-database-portal.md) 만들기

## <a name="enable-streaming-ingestion-on-your-cluster"></a>클러스터에서 스트리밍 수집 사용

### <a name="enable-streaming-ingestion-while-creating-a-new-cluster-in-the-azure-portal"></a>Azure Portal에서 새 클러스터를 만드는 동안 스트리밍 수집을 사용 하도록 설정

새 Azure 데이터 탐색기 클러스터를 만드는 동안 스트리밍 수집을 사용 하도록 설정할 수 있습니다. 

**구성** 탭에서 **스트리밍**수집  >  **켜기**를 선택 합니다.

:::image type="content" source="media/ingest-data-streaming/cluster-creation-enable-streaming.png" alt-text="Azure 데이터 탐색기에서 클러스터를 만드는 동안 스트리밍 수집 사용":::

### <a name="enable-streaming-ingestion-on-an-existing-cluster-in-the-azure-portal"></a>Azure Portal에서 기존 클러스터에 대 한 스트리밍 수집 사용

1. Azure Portal에서 Azure Data Explorer 클러스터로 이동합니다. 
1. **설정**에서 **구성**을 선택 합니다. 
1. **구성** 창에서 **켜기** 를 선택 하 여 **스트리밍**수집을 사용 하도록 설정 합니다.
1. **저장**을 선택합니다.

    :::image type="content" source="media/ingest-data-streaming/streaming-ingestion-on.png" alt-text="Azure 데이터 탐색기에서 스트리밍 수집 켜기":::

> [!WARNING]
> 스트리밍 수집을 사용 하도록 설정 하기 전에 [제한 사항을](#limitations) 검토 합니다.

## <a name="create-a-target-table-and-define-the-policy-in-the-azure-portal"></a>대상 테이블을 만들고 Azure Portal에서 정책을 정의 합니다.

1. Azure Portal에서 클러스터로 이동 합니다.
1. **쿼리**를 선택합니다.

    :::image type="content" source="media/ingest-data-streaming/cluster-select-query-tab.png" alt-text="Azure 데이터 탐색기 포털에서 쿼리를 선택 하 여 스트리밍 수집을 사용 하도록 설정 합니다.":::

1. 스트리밍 수집을 통해 데이터를 수신 하는 테이블을 만들려면 **쿼리 창** 에 다음 명령을 복사 하 고 **실행**을 선택 합니다.

    ```Kusto
    .create table TestTable (TimeStamp: datetime, Name: string, Metric: int, Source:string)
    ```

    :::image type="content" source="media/ingest-data-streaming/create-table.png" alt-text="Azure 데이터 탐색기에 수집을 스트리밍하는 테이블 만들기":::

1. 만든 테이블이 나이 테이블이 포함 된 데이터베이스에 대 한 [스트리밍 수집 정책을](kusto/management/streamingingestionpolicy.md) 정의 합니다. 
 
    > [!TIP]
    > 데이터베이스 수준에서 정의 된 정책은 데이터베이스의 모든 기존 및 미래의 테이블에 적용 됩니다. 
    
1. 다음 명령 중 하나를 **쿼리 창** 에 복사 하 고 **실행**을 선택 합니다.

    ```kusto
    .alter table TestTable policy streamingingestion enable
    ```

    또는

    ```kusto
    .alter database StreamingTestDb policy streamingingestion enable
    ```

    :::image type="content" source="media/ingest-data-streaming/define-streaming-ingestion-policy.png" alt-text="Azure 데이터 탐색기에서 스트리밍 수집 정책 정의":::

[!INCLUDE [ingest-data-streaming-use](includes/ingest-data-streaming-types.md)]

[!INCLUDE [ingest-data-streaming-disable](includes/ingest-data-streaming-disable.md)]

## <a name="drop-the-streaming-ingestion-policy-in-the-azure-portal"></a>Azure Portal에서 스트리밍 수집 정책을 삭제 합니다.

1. Azure Portal에서 Azure 데이터 탐색기 클러스터로 이동 하 고 **쿼리**를 선택 합니다. 
1. 테이블에서 스트리밍 수집 정책을 삭제 하려면 다음 명령을 **쿼리 창** 에 복사 하 고 **실행**을 선택 합니다.

    ```Kusto
    .delete table TestTable policy streamingingestion 
    ```

    :::image type="content" source="media/ingest-data-streaming/delete-streaming-ingestion-policy.png" alt-text="Azure 데이터 탐색기에서 스트리밍 수집 정책 삭제":::

1. **설정**에서 **구성**을 선택 합니다.
1. **구성** 창에서 **끄기** 를 선택 하 여 **스트리밍**수집을 사용 하지 않도록 설정 합니다.
1. **저장**을 선택합니다.

    :::image type="content" source="media/ingest-data-streaming/streaming-ingestion-off.png" alt-text="Azure 데이터 탐색기에서 스트리밍 수집 해제":::

[!INCLUDE [ingest-data-streaming-limitations](includes/ingest-data-streaming-limitations.md)]

## <a name="next-steps"></a>다음 단계

* [Azure 데이터 탐색기에서 데이터 쿼리](web-query-data.md)
