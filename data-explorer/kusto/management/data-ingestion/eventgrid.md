---
title: Event Grid 구독을 사용 하 여 저장소에서 수집-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 Event Grid 구독을 사용 하 여 저장소에서 수집 하는 방법을 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 07/01/2020
ms.openlocfilehash: b8b72c2bc3b34f6d42ea71903272893ffde773fc
ms.sourcegitcommit: 4507466bdcc7dd07e6e2a68c0707b6226adc25af
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87106464"
---
# <a name="ingest-from-storage-using-event-grid-subscription"></a>Event Grid 구독을 사용하여 스토리지에서 수집

Azure 데이터 탐색기는 blob 생성 알림에 대 한 [Azure Event Grid](/azure/event-grid/overview) 구독과 Azure Storage (blob Storage 및 ADLSv2)에서 지속적인 수집을 제공 하 고 이벤트 허브를 통해 이러한 알림을 Azure 데이터 탐색기로 스트리밍합니다.

## <a name="data-format"></a>데이터 형식

* Blob은 [지원 되는 형식](../../../ingestion-supported-formats.md)중 하나일 수 있습니다.
* Blob는 압축할 수 있습니다. 자세한 내용은 [지원 되는 압축](../../../ingestion-supported-formats.md#supported-data-compression-formats)을 참조 하세요.

> [!NOTE]
> 이상적인 원래 압축 되지 않은 데이터 크기는 blob 메타 데이터의 일부 여야 합니다.
> 압축 되지 않은 크기가 지정 되지 않은 경우에는 Azure 데이터 탐색기에서 파일 크기에 따라 예측 합니다. `rawSizeBytes`Blob 메타 데이터의 [속성](#ingestion-properties) 을 압축 되지 않은 데이터 크기 (바이트)로 설정 하 여 원래 데이터 크기를 제공할 수 있습니다.
> 
> 4GB 파일 당 수집 된 압축 되지 않은 크기 제한이 있습니다.

## <a name="ingestion-properties"></a>수집 속성

Blob 메타 데이터를 통해 blob 수집의 수집 [속성](../../../ingestion-properties.md) 을 지정할 수 있습니다.
다음 속성을 설정할 수 있습니다.

|속성 | 설명|
|---|---|
| rawSizeBytes | 원시 (압축 되지 않은) 데이터의 크기입니다. Avro/ORC/Parquet의 경우이 값은 서식 지정 압축을 적용 하기 전의 크기입니다.|
| kustoTable |  기존 대상 테이블의 이름입니다. `Table`블레이드의 집합을 재정의 `Data Connection` 합니다. |
| kustoDataFormat |  데이터 형식입니다. **데이터 연결** 블레이드에 설정 된 **데이터 형식을** 덮어씁니다. |
| kustoIngestionMappingReference |  사용할 기존 수집 매핑의 이름입니다. **데이터 연결** 블레이드의 **열 매핑** 집합을 덮어씁니다.|
| kustoIgnoreFirstRecord | 로 설정 `true` 되 면 Azure 데이터 탐색기는 blob의 첫 번째 행을 무시 합니다. 테이블 형식 데이터 (CSV, TSV 또는 이와 유사한)를 사용 하 여 헤더를 무시 합니다. |
| kustoExtentTags | 결과 범위에 첨부 될 [태그](../extents-overview.md#extent-tagging) 를 나타내는 문자열입니다. |
| kustoCreationTime |  ISO 8601 문자열로 형식이 지정 된 blob에 대 한 [$IngestionTime](../../query/ingestiontimefunction.md?pivots=azuredataexplorer) 를 재정의 합니다. 백필에 사용 합니다. |

## <a name="events-routing"></a>이벤트 라우팅

Azure 데이터 탐색기 클러스터에 대 한 blob 저장소 연결을 설정할 때 대상 테이블 속성을 지정 합니다.
* 테이블 이름
* 데이터 형식
* 매핑

이 설정은 데이터에 대 한 기본 라우팅으로, 라고도 `static routing` 합니다.
Blob 메타 데이터를 사용 하 여 각 blob에 대 한 대상 테이블 속성을 지정할 수도 있습니다. 수집 [속성](#ingestion-properties)에 지정 된 대로 데이터가 동적으로 라우팅됩니다.

다음은 blob 메타 데이터를 업로드 하기 전에 수집 속성을 blob 메타 데이터에 설정 하는 예입니다. Blob은 다른 테이블로 라우팅됩니다.

데이터를 생성 하는 방법에 대 한 자세한 내용은 [샘플 코드](#generating-data)를 참조 하세요.

```csharp
// Blob is dynamically routed to table `Events`, ingested using `EventsMapping` data mapping
blob = container.GetBlockBlobReference(blobName2);
blob.Metadata.Add("rawSizeBytes", "4096‬"); // the uncompressed size is 4096 bytes
blob.Metadata.Add("kustoTable", "Events");
blob.Metadata.Add("kustoDataFormat", "json");
blob.Metadata.Add("kustoIngestionMappingReference", "EventsMapping");
blob.UploadFromFile(jsonCompressedLocalFileName);
```

## <a name="create-an-event-grid-subscription-in-your-storage-account"></a>스토리지 계정에 Event Grid 구독 만들기

> [!NOTE]
> 최상의 성능을 위해 Azure 데이터 탐색기 클러스터와 동일한 지역에 모든 리소스를 만듭니다.

### <a name="prerequisites"></a>필수 구성 요소

* [스토리지 계정 만들기](/azure/storage/common/storage-quickstart-create-account)
  또는 종류에 대 한 Azure Storage 계정에 Event Grid 알림 구독을 설정할 수 있습니다 `BlobStorage` `StorageV2` .
  [Data Lake Storage Gen2](/azure/storage/blobs/data-lake-storage-introduction) 사용도 지원 됩니다.
* [이벤트 허브를 만듭니다](/azure/event-hubs/event-hubs-create).

### <a name="event-grid-subscription"></a>Event Grid 구독
 
1. Azure Portal에서 스토리지 계정을 찾습니다.
1. 왼쪽 메뉴에서 **이벤트**  >  **이벤트 구독**을 선택 합니다.

     :::image type="content" source="../images/eventgrid/create-event-grid-subscription-1.png" alt-text="Event grid 구독 만들기":::

1. **기본** 탭의 **이벤트 구독 만들기** 창에 다음 값을 제공합니다.

    :::image type="content" source="../images/eventgrid/create-event-grid-subscription-2.png" alt-text="입력할 이벤트 구독 값 만들기":::

    |**설정** | **제안 값** | **필드 설명**|
    |---|---|---|
    | 이름 | *test-grid-connection* | 만들려는 event grid 구독의 이름입니다.|
    | 이벤트 스키마 | *Event Grid 스키마* | Event Grid에 사용해야 하는 스키마입니다. |
    | 항목 종류 | *스토리지 계정* | Event Grid 항목의 종류입니다. |
    | 원본 리소스 | *gridteststorage1* | 사용자 스토리지 계정의 이름입니다. |
    | 시스템 토픽 이름 | *gridteststorage1...* | Azure Storage 이벤트를 게시 하는 시스템 항목입니다. 그런 다음이 시스템 항목은 이벤트를 수신 하 고 처리 하는 구독자로 이벤트를 전달 합니다. |
    | 이벤트 유형으로 필터링 | *만든 Blob* | 알림을 받을 특정 이벤트 현재 지원 되는 유형은 Microsoft. 저장소입니다. 구독을 만들 때 선택 해야 합니다.|
    | 엔드포인트 유형 | *Event Hubs* | 이벤트를 보낼 엔드포인트 유형 |
    | 엔드포인트 | *test-hub* | 앞에서 만든 이벤트 허브입니다. |

1. 특정 주제를 추적 하려면 **필터** 탭을 선택 합니다. 알림에 대한 필터를 다음과 같이 설정합니다.
   * **제목 필터링 사용**을 선택합니다.
   * **제목 시작** 필드는 주체의 *리터럴* 접두사입니다. 적용 되는 패턴은 *startswith*이므로 여러 컨테이너, 폴더 또는 blob에 걸쳐 있을 수 있습니다. 와일드카드는 허용되지 않습니다.
       * Blob 컨테이너에 대 한 필터를 정의 하려면 필드를 다음과 같이 설정 *해야* 합니다 *`/blobServices/default/containers/[container prefix]`* .
       * Blob 접두사 (또는 Azure Data Lake Gen2의 폴더)에 대 한 필터를 정의 하려면 필드를 다음과 같이 설정 *해야* 합니다 *`/blobServices/default/containers/[container name]/blobs/[folder/blob prefix]`* .
   * **제목 종료 문자** 필드는 Blob의 *리터럴* 접미사입니다. 와일드카드는 허용되지 않습니다.
   * **대/소문자 구분 주체 일치** 필드 접두사 및 접미사 필터에서 대/소문자를 구분 하는지 여부를 나타냅니다.
   * 이벤트 필터링에 대 한 자세한 내용은 [Blob storage 이벤트](/azure/storage/blobs/storage-blob-event-overview#filtering-events) 를 참조 하세요.
    
        :::image type="content" source="../images/eventgrid/filters-tab.png" alt-text="필터 탭 이벤트 표":::

> [!NOTE]
> 끝점이 이벤트 수신을 승인 하지 않으면 Azure Event Grid는 재시도 메커니즘을 활성화 합니다. 이 재시도 배달이 실패 하면 배달 *못*한 편지 처리를 사용 하 여 배달 되지 않은 이벤트를 저장소 계정으로 배달할 수 Event Grid. 자세한 내용은 [Event Grid 메시지 배달 및 재시도](/azure/event-grid/delivery-and-retry#retry-schedule-and-duration)를 참조하세요.

### <a name="data-ingestion-connection-to-azure-data-explorer"></a>Azure 데이터 탐색기에 대 한 데이터 수집 연결

* Azure Portal를 통해 [Azure 데이터 탐색기에서 Event Grid 데이터 연결을 만듭니다](../../../ingest-data-event-grid.md#create-an-event-grid-data-connection-in-azure-data-explorer).
* Kusto 관리 .NET SDK 사용: [Event Grid 데이터 연결 추가](../../../data-connection-event-grid-csharp.md#add-an-event-grid-data-connection)
* Kusto management Python SDK를 사용 하 여 [Event Grid 데이터 연결 추가](../../../data-connection-event-grid-python.md#add-an-event-grid-data-connection)
* [Event Grid 데이터 연결을 추가 하기 위한 Azure Resource Manager 템플릿](../../../data-connection-event-grid-resource-manager.md#azure-resource-manager-template-for-adding-an-event-grid-data-connection) 사용

### <a name="generating-data"></a>데이터 생성

> [!NOTE]
> * `BlockBlob`를 사용 하 여 데이터를 생성 합니다. `AppendBlob`은 지원되지 않습니다.

다음은 로컬 파일에서 blob을 만들고 수집 속성을 blob 메타 데이터로 설정 하 고 업로드 하는 예입니다.

 ```csharp
 var azureStorageAccountConnectionString=<storage_account_connection_string>;

var containerName=<container_name>;
var blobName=<blob_name>;
var localFileName=<file_to_upload>;

// Creating the container
var azureStorageAccount = CloudStorageAccount.Parse(azureStorageAccountConnectionString);
var blobClient = azureStorageAccount.CreateCloudBlobClient();
var container = blobClient.GetContainerReference(containerName);
container.CreateIfNotExists();

// Set ingestion properties in blob's metadata & uploading the blob
var blob = container.GetBlockBlobReference(blobName);
blob.Metadata.Add("rawSizeBytes", "4096‬"); // the uncompressed size is 4096 bytes
blob.Metadata.Add("kustoIgnoreFirstRecord", "true"); // First line of this csv file are headers
blob.UploadFromFile(csvCompressedLocalFileName);
```

> [!NOTE]
> Azure Data Lake Gen2 storage SDK를 사용 하려면 `CreateFile` 파일을 업로드 하 고 `Flush` close 매개 변수가 "true"로 설정 된 끝에를 사용 해야 합니다.
> Data Lake Gen2 SDK를 올바르게 사용 하는 방법에 대 한 자세한 예제는 [AZURE DATA LAKE sdk를 사용 하 여 파일 업로드](../../../data-connection-event-grid-csharp.md#upload-file-using-azure-data-lake-sdk)를 참조 하세요.

## <a name="blob-lifecycle"></a>Blob 수명 주기

Azure 데이터 탐색기은 수집 후 blob을 삭제 하지 않습니다. [Azure blob 저장소 수명 주기](/azure/storage/blobs/storage-lifecycle-management-concepts?tabs=azure-portal) 를 사용 하 여 blob 삭제를 관리 합니다. 3 ~ 5 일간 blob을 유지 하는 것이 좋습니다.

## <a name="known-issues"></a>알려진 문제

* Azure 데이터 탐색기을 사용 하 여 event grid 수집에 사용 되는 파일을 [내보내는](../data-export/export-data-to-storage.md) 경우 다음을 참고 하세요. 
    * 내보내기 명령에 제공 된 연결 문자열이 나 [외부 테이블](../data-export/export-data-to-an-external-table.md) 에 제공 된 연결 문자열이 [ADLS Gen2 형식](../../api/connection-strings/storage.md#azure-data-lake-store) (예:)의 연결 문자열이 고 `abfss://filesystem@accountname.dfs.core.windows.net` 저장소 계정에서 계층적 네임 스페이스를 사용할 수 없는 경우 Event Grid 알림이 트리거되지 않습니다. 
    * 계층 구조 네임 스페이스에 대해 계정을 사용 하지 않는 경우 연결 문자열은 [Blob Storage](../../api/connection-strings/storage.md#azure-storage-blob) 형식을 사용 해야 합니다 (예: `https://accountname.blob.core.windows.net` ). 내보내기는 ADLS Gen2 연결 문자열을 사용 하는 경우에도 정상적으로 작동 하지만 알림이 트리거되지 않으며 Event Grid 수집은 작동 하지 않습니다.
