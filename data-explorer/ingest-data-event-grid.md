---
title: Azure Data Explorer에 Azure Blob 수집
description: 이 문서에서는 Event Grid 구독을 사용 하 여 Azure 데이터 탐색기에 저장소 계정 데이터를 보내는 방법에 대해 알아봅니다.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: how-to
ms.date: 08/13/2020
ms.openlocfilehash: 3452ca547778869ae08e7aef92c1a3a7a4754446
ms.sourcegitcommit: 58588ba8d1fc5a6adebdce2b556db5bc542e38d8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2020
ms.locfileid: "92098441"
---
# <a name="ingest-blobs-into-azure-data-explorer-by-subscribing-to-event-grid-notifications"></a>Event Grid 알림을 구독하여 Azure Data Explorer에 Blob 수집

> [!div class="op_single_selector"]
> * [포털](ingest-data-event-grid.md)
> * [C#](data-connection-event-grid-csharp.md)
> * [Python](data-connection-event-grid-python.md)
> * [Azure Resource Manager 템플릿](data-connection-event-grid-resource-manager.md)

[!INCLUDE [data-connector-intro](includes/data-connector-intro.md)]

이 문서에서는 Event Grid 데이터 연결을 사용 하 여 저장소 계정에서 Azure 데이터 탐색기로 blob을 수집 하는 방법에 대해 알아봅니다. [Azure Event Grid](/azure/event-grid/overview) 구독을 설정 하는 Event Grid 데이터 연결을 만듭니다. Event Grid 구독은 Azure 이벤트 허브를 통해 저장소 계정에서 Azure 데이터 탐색기로 이벤트를 라우팅합니다. 그런 다음 시스템 전체의 데이터 흐름에 대 한 예를 볼 수 있습니다. 

Event Grid에서 Azure 데이터 탐색기에 수집에 대 한 일반 정보는 [Event Grid에 연결](ingest-data-event-grid-overview.md)을 참조 하세요. Azure Portal에서 리소스를 수동으로 만들려면 [Event Grid 수집에 대 한 리소스 수동 만들기](ingest-data-event-grid-manual.md)를 참조 하세요.

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독 [무료 Azure 계정](https://azure.microsoft.com/free/)을 만듭니다.
* [클러스터 및 데이터베이스](create-cluster-database-portal.md)
* [저장소 계정](/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal).

## <a name="create-a-target-table-in-azure-data-explorer"></a>Azure 데이터 탐색기에서 대상 테이블 만들기

Azure Data Explorer에서 Event Hubs가 데이터를 보낼 테이블을 만듭니다. 필수 구성 요소에서 준비한 클러스터와 데이터베이스에서 테이블을 만듭니다.

1. Azure Portal의 클러스터 아래에서 **쿼리**를 선택합니다.

    :::image type="content" source="media/ingest-data-event-grid/query-explorer-link.png" alt-text="쿼리 탐색기 링크"::: 

1. 다음 명령을 창에 복사하고, **실행**을 선택하여 수집된 데이터를 받을 테이블(TestTable)을 만듭니다.

    ```kusto
    .create table TestTable (TimeStamp: datetime, Value: string, Source:string)
    ```

    :::image type="content" source="media/ingest-data-event-grid/run-create-table.png" alt-text="쿼리 탐색기 링크":::

1. 다음 명령을 창에 복사하고, **실행**을 선택하여 들어오는 JSON 데이터를 테이블(TestTable)의 열 이름과 데이터 형식에 매핑합니다.

    ```kusto
    .create table TestTable ingestion json mapping 'TestMapping' '[{"column":"TimeStamp","path":"$.TimeStamp"},{"column":"Value","path":"$.Value"},{"column":"Source","path":"$.Source"}]'
    ```

## <a name="create-an-event-grid-data-connection-in-azure-data-explorer"></a>Azure Event Grid에서 Event Grid 데이터 연결 만들기

이제 저장소 계정을 Azure 데이터 탐색기에 연결 하 여 저장소로 흐르는 데이터가 테스트 테이블로 스트리밍됩니다. 

1. 만든 클러스터에서 **데이터베이스**  >  **testdatabase**를 선택 합니다.

    :::image type="content" source="media/ingest-data-event-grid/select-test-database.png" alt-text="쿼리 탐색기 링크":::

1. **데이터**수집  >  **데이터 연결 추가**를 선택 합니다.

    :::image type="content" source="media/ingest-data-event-grid/data-ingestion-create.png" alt-text="쿼리 탐색기 링크":::

### <a name="data-connection---basics-tab"></a>데이터 연결-기본 사항 탭

1. 연결 유형: **Blob storage**를 선택 합니다.

1. 다음 정보로 양식을 작성합니다.

    :::image type="content" source="media/ingest-data-event-grid/data-connection-basics.png" alt-text="쿼리 탐색기 링크":::

    |**설정** | **제안 값** | **필드 설명**|
    |---|---|---|
    | 데이터 연결 이름 | *test-grid-connection* | Azure Data Explorer에서 만들 연결의 이름입니다.|
    | 스토리지 계정 구독 | 구독 ID | 저장소 계정이 있는 구독 ID입니다.|
    | 스토리지 계정 | *gridteststorage1* | 이전에 만든 스토리지 계정의 이름입니다.|
    | 이벤트 유형 | *만든 blob* 또는 *blob의 이름을 바꿨습니다* . | 수집을 트리거하는 이벤트의 유형입니다. |
    | 리소스 만들기 | *자동* | Azure 데이터 탐색기에서 Event Grid 구독, 이벤트 허브 네임 스페이스 및 이벤트 허브를 만들지 여부를 정의 합니다. 리소스를 수동으로 만들려면 Event Grid 수집을 [위한 리소스 수동 만들기](ingest-data-event-grid-manual.md) 를 참조 하세요.|

1. 특정 주제를 추적 하려면 **필터 설정** 을 선택 합니다. 알림에 대한 필터를 다음과 같이 설정합니다.
    * **접두사** 필드는 주체의 *리터럴* 접두사입니다. 적용 되는 패턴은 *startswith*여러 컨테이너, 폴더 또는 blob에 걸쳐 있을 수 있습니다. 와일드카드는 허용되지 않습니다.
        * Blob 컨테이너에 대 한 필터를 정의 하려면 필드를 다음과 같이 설정 *해야* 합니다 *`/blobServices/default/containers/[container prefix]`* .
        * Blob 접두사 (또는 Azure Data Lake Gen2의 폴더)에 대 한 필터를 정의 하려면 필드를 다음과 같이 설정 *해야* 합니다 *`/blobServices/default/containers/[container name]/blobs/[folder/blob prefix]`* .
    * **접미사** 필드는 blob의 *리터럴* 접미사입니다. 와일드카드는 허용되지 않습니다.
    * **대/소문자 구분** 필드는 접두사 및 접미사 필터에서 대/소문자를 구분 하는지 여부를 나타냅니다.
    * 이벤트를 필터링 하는 방법에 대 한 자세한 내용은 [Blob storage 이벤트](/azure/storage/blobs/storage-blob-event-overview#filtering-events)를 참조 하세요.
    
    :::image type="content" source="media/ingest-data-event-grid/filter-settings.png" alt-text="쿼리 탐색기 링크":::    

1. **다음: 수집 속성**을 선택 합니다.

### <a name="data-connection---ingest-properties-tab"></a>데이터 연결-수집 속성 탭

1. 다음 정보로 양식을 작성합니다. 테이블 및 매핑 이름은 대/소문자를 구분 합니다.

   :::image type="content" source="media/ingest-data-event-grid/data-connection-ingest-properties.png" alt-text="쿼리 탐색기 링크":::

    수집 속성:

     **설정** | **제안 값** | **필드 설명**
    |---|---|---|
    | 테이블 이름 | *TestTable* | **TestDatabase**에 만든 테이블입니다. |
    | 데이터 형식 | *JSON* | 지원 되는 형식은 Avro, CSV, JSON, MULTILINE JSON, ORC, PARQUET, PSV, SCSV, SOHSV, TSV, TXT, TSVE, APACHEAVRO, RAW 및 W3CLOG입니다. 지원 되는 압축 옵션은 Zip 및 GZip입니다. |
    | 매핑 | *TestMapping* | **TestDatabase**에서 생성된 것으로, 들어오는 JSON 데이터를 **TestTable**의 열 이름 및 데이터 형식에 매핑.|
    | 고급 설정 | *내 데이터에 머리글 있음* | 헤더를 무시 합니다. * SV 형식 파일에 대해 지원 됩니다.|

   > [!NOTE]
   > 모든 **기본 라우팅 설정을**지정할 필요는 없습니다. 부분 설정도 허용 됩니다.
1. 다음을 선택 합니다 **. 검토 + 만들기**

### <a name="data-connection---review--create-tab"></a>데이터 연결-검토 + 만들기 탭

1. 자동으로 생성 된 리소스를 검토 하 고 **만들기**를 선택 합니다.

    :::image type="content" source="media/ingest-data-event-grid/create-event-grid-data-connection-review-create.png" alt-text="쿼리 탐색기 링크":::

### <a name="deployment"></a>배포

배포가 완료될 때까지 기다립니다. 배포에 실패 한 경우 실패 한 단계 옆에 있는 **작업 세부** 정보를 선택 하 여 실패 원인에 대 한 자세한 정보를 가져옵니다. 다시 **배포를 선택 하** 여 리소스 배포를 다시 시도 합니다. 배포 하기 전에 매개 변수를 변경할 수 있습니다.

:::image type="content" source="media/ingest-data-event-grid/deploy-event-grid-resources.png" alt-text="쿼리 탐색기 링크":::

## <a name="generate-sample-data"></a>샘플 데이터 생성

이제 Azure 데이터 탐색기와 저장소 계정이 연결 되었으므로 샘플 데이터를 만들어 저장소 컨테이너에 업로드할 수 있습니다.

Azure Storage 리소스와 상호 작용하는 몇 가지 기본 Azure CLI 명령을 발급하는 작은 셸 스크립트를 사용합니다. 이 스크립트는 다음 작업을 수행 합니다. 
1. 저장소 계정에 새 컨테이너를 만듭니다.
1. 기존 파일 (blob)을 해당 컨테이너에 업로드 합니다.
1. 컨테이너의 blob을 나열 합니다. 

[Azure Cloud Shell](/azure/cloud-shell/overview)을 사용하여 포털에서 직접 스크립트를 실행할 수 있습니다.

파일에 데이터를 저장하고 이 스크립트를 사용하여 업로드합니다.

```json
{"TimeStamp": "1987-11-16 12:00","Value": "Hello World","Source": "TestSource"}
```

```azurecli
#!/bin/bash
### A simple Azure Storage example script

    export AZURE_STORAGE_ACCOUNT=<storage_account_name>
    export AZURE_STORAGE_KEY=<storage_account_key>

    export container_name=<container_name>
    export blob_name=<blob_name>
    export file_to_upload=<file_to_upload>
    export destination_file=<destination_file>

    echo "Creating the container..."
    az storage container create --name $container_name

    echo "Uploading the file..."
    az storage blob upload --container-name $container_name --file $file_to_upload --name $blob_name --metadata "rawSizeBytes=1024"

    echo "Listing the blobs..."
    az storage blob list --container-name $container_name --output table

    echo "Done"
```

> [!NOTE]
> 최상의 수집 성능을 얻으려면 수집을 위해 전송 된 압축 blob *의 압축 되지 않은 크기를* 전달 해야 합니다. Event Grid 알림에는 기본 정보만 포함 되어 있으므로 크기 정보를 명시적으로 전달 해야 합니다. 압축 되지 않은 크기 정보는 blob 메타 데이터의 `rawSizeBytes` 속성을 *압축* 되지 않은 데이터 크기 (바이트)로 설정 하 여 제공할 수 있습니다.

### <a name="ingestion-properties"></a>수집 속성

Blob 메타 데이터를 통해 blob 수집의 수집 [속성](ingest-data-event-grid-overview.md#set-ingestion-properties) 을 지정할 수 있습니다. 

> [!NOTE]
> Azure 데이터 탐색기는 blob 사후 수집을 삭제 하지 않습니다.
> 3 ~ 5 일에 대 한 blob을 유지 합니다.
> Blob 삭제를 관리 하려면 [Azure blob 저장소 수명 주기](/azure/storage/blobs/storage-lifecycle-management-concepts?tabs=azure-portal) 를 사용 합니다. 

## <a name="review-the-data-flow"></a>데이터 흐름 검토

> [!NOTE]
> Azure Data Explorer에는 데이터 수집을 위한 집계(일괄 처리) 정책이 있으며, 이는 수집 프로세스를 최적화하도록 설계되었습니다.
기본적으로 정책은 5 분으로 구성됩니다.
필요한 경우 나중에 정책을 변경할 수 있습니다. 이 문서에서는 몇 분의 대기 시간을 예측할 수 있습니다.

1. Azure Portal의 Event Grid에서 앱이 실행되는 동안 작업이 급증하는 것을 볼 수 있습니다.

    :::image type="content" source="media/ingest-data-event-grid/event-grid-graph.png" alt-text="쿼리 탐색기 링크":::

1. 현재까지 데이터베이스로 전송된 메시지의 수를 확인하려면 테스트 데이터베이스에서 다음 쿼리를 실행합니다.

    ```kusto
    TestTable
    | count
    ```

1. 메시지 내용을 확인하려면 테스트 데이터베이스에서 다음 쿼리를 실행합니다.

    ```kusto
    TestTable
    ```

    결과 집합은 다음 이미지와 같습니다.

    :::image type="content" source="media/ingest-data-event-grid/table-result.png" alt-text="쿼리 탐색기 링크":::

## <a name="clean-up-resources"></a>리소스 정리

Event grid를 다시 사용 하지 않으려는 경우에는 자동으로 생성 된 Event Grid 구독, 이벤트 허브 네임 스페이스 및 이벤트 허브를 정리 하 여 비용을 발생 시 키 지 않도록 합니다.

1. Azure Portal에서 왼쪽 메뉴로 이동 하 여 **모든 리소스**를 선택 합니다.

    :::image type="content" source="media/ingest-data-event-grid/clean-up-resources-select-all-resource.png" alt-text="쿼리 탐색기 링크":::    

1. 이벤트 허브 네임 스페이스를 검색 하 고 **삭제** 를 선택 하 여 삭제 합니다.

    :::image type="content" source="media/ingest-data-event-grid/clean-up-resources-find-eventhub-namespace-delete.png" alt-text="쿼리 탐색기 링크":::

1. 리소스 삭제 양식에서 삭제를 확인 하 여 이벤트 허브 네임 스페이스 및 이벤트 허브 리소스를 삭제 합니다.

1. 스토리지 계정으로 이동합니다. 왼쪽 메뉴에서 **이벤트**를 선택 합니다.

    :::image type="content" source="media/ingest-data-event-grid/clean-up-resources-select-events.png" alt-text="쿼리 탐색기 링크":::

1. 그래프 아래에서 Event Grid 구독을 선택한 다음 **삭제** 를 선택 하 여 삭제 합니다.

    :::image type="content" source="media/ingest-data-event-grid/delete-event-grid-subscription.png" alt-text="쿼리 탐색기 링크":::

1. Event Grid 데이터 연결을 삭제 하려면 Azure 데이터 탐색기 클러스터로 이동 합니다. 왼쪽 메뉴에서 **데이터베이스**를 선택 합니다.

1. 데이터베이스 **Testdatabase**를 선택 합니다.

    :::image type="content" source="media/ingest-data-event-grid/clean-up-resources-select-database.png" alt-text="쿼리 탐색기 링크":::

1. 왼쪽 메뉴에서 **데이터**수집을 선택 합니다.

    :::image type="content" source="media/ingest-data-event-grid/clean-up-resources-select-data-ingestion.png" alt-text="쿼리 탐색기 링크":::

1. 데이터 연결 *테스트-표-연결* 을 선택한 다음 **삭제** 를 선택 하 여 삭제 합니다.

## <a name="next-steps"></a>다음 단계

* [Azure 데이터 탐색기에서 데이터 쿼리](web-query-data.md)
