---
title: Event Grid 구독을 사용 하 여 저장소에서 수집-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기에서 Event Grid 구독을 사용 하 여 저장소에서 수집 하는 방법을 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/01/2020
ms.openlocfilehash: 43012752889d534d8f74943cfa6c528b2c72cd8b
ms.sourcegitcommit: 8e097319ea989661e1958efaa1586459d2b69292
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/15/2020
ms.locfileid: "84780646"
---
# <a name="ingest-from-storage-using-event-grid-subscription"></a>Event Grid 구독을 사용하여 스토리지에서 수집

Azure 데이터 탐색기는 blob 생성 알림에 대 한 [Azure Event Grid](https://docs.microsoft.com/azure/event-grid/overview) 구독을 사용 하 여 Azure Storage (blob Storage 및 ADLSv2)에서 지속적인 수집을 제공 하 고 이벤트 허브를 통해 이러한 알림을 kusto로 스트리밍합니다.

## <a name="data-format"></a>데이터 형식

* Blob은 [지원 되는 형식](../../../ingestion-supported-formats.md)중 하나일 수 있습니다.
* Blob은 [지원 되는 압축](../../../ingestion-supported-formats.md#supported-data-compression-formats) 에서 압축할 수 있습니다.

> [!NOTE]
> 이상적인 원래 압축 되지 않은 데이터 크기는 blob 메타 데이터의 일부 여야 합니다.
> 압축 되지 않은 크기를 지정 하지 않으면 Azure 데이터 탐색기는 파일 크기에 따라 예측 합니다. `rawSizeBytes`Blob 메타 데이터의 [속성](#ingestion-properties) 을 **압축** 되지 않은 데이터 크기 (바이트)로 설정 하 여 원래 데이터 크기를 제공 합니다.
> 4GB 파일 당 수집 된 압축 되지 않은 크기 제한이 있음을 확인 하세요.

## <a name="ingestion-properties"></a>수집 속성

Blob 메타 데이터를 통해 blob 수집의 수집 [속성](../../../ingestion-properties.md) 을 지정할 수 있습니다.
다음 속성을 설정할 수 있습니다.

|속성 | Description|
|---|---|
| rawSizeBytes | 원시 (압축 되지 않은) 데이터의 크기입니다. Avro/ORC/Parquet의 경우이 값은 서식 지정 압축을 적용 하기 전의 크기입니다.|
| kustoTable |  기존 대상 테이블의 이름입니다. `Table`블레이드의 집합을 재정의 `Data Connection` 합니다. |
| kustoDataFormat |  데이터 형식입니다. `Data format`블레이드의 집합을 재정의 `Data Connection` 합니다. |
| kustoIngestionMappingReference |  사용할 기존 수집 매핑의 이름입니다. `Column mapping`블레이드의 집합을 재정의 `Data Connection` 합니다.|
| kustoIgnoreFirstRecord | 로 설정 `true` 되 면 Azure 데이터 탐색기는 blob의 첫 번째 행을 무시 합니다. 테이블 형식 데이터 (CSV, TSV 또는 이와 유사한)를 사용 하 여 헤더를 무시 합니다. |
| kustoExtentTags | 결과 범위에 첨부 될 [태그](../extents-overview.md#extent-tagging) 를 나타내는 문자열입니다. |
| kustoCreationTime |  ISO 8601 문자열로 형식이 지정 된 blob에 대 한 [$IngestionTime](../../query/ingestiontimefunction.md?pivots=azuredataexplorer) 를 재정의 합니다. 백필에 사용 합니다. |

## <a name="events-routing"></a>이벤트 라우팅

Azure 데이터 탐색기 클러스터에 대 한 blob 저장소 연결을 설정할 때 대상 테이블 속성 (테이블 이름, 데이터 형식 및 매핑)을 지정 합니다. 이 설정은 데이터에 대 한 기본 라우팅으로, 라고도 `static routing` 합니다.
Blob 메타 데이터를 사용 하 여 각 blob에 대 한 대상 테이블 속성을 지정할 수도 있습니다. 수집 [속성](#ingestion-properties)에 지정 된 대로 데이터를 동적으로 라우팅합니다.

다음은 blob 메타 데이터를 업로드 하기 전에 수집 속성을 blob 메타 데이터에 설정 하는 예입니다. Blob은 다른 테이블로 라우팅됩니다.

데이터를 생성 하는 방법에 대 한 자세한 내용은 [샘플 코드](#generating-data) 를 참조 하세요.

 ```csharp
// Blob is dynamically routed to table `Events`, ingested using `EventsMapping` data mapping
blob = container.GetBlockBlobReference(blobName2);
blob.Metadata.Add("rawSizeBytes", "4096‬"); // the uncompressed size is 4096 bytes
blob.Metadata.Add("kustoTable", "Events");
blob.Metadata.Add("kustoDataFormat", "json");
blob.Metadata.Add("kustoIngestionMappingReference", "EventsMapping");
blob.UploadFromFile(jsonCompressedLocalFileName);
```

## <a name="create-event-grid-subscription"></a>Event Grid 구독 만들기

> [!Note]
> 최상의 성능을 위해 Azure 데이터 탐색기 클러스터와 동일한 지역에 모든 리소스를 만듭니다.

### <a name="prerequisites"></a>사전 요구 사항

* [스토리지 계정 만들기](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) 
  또는 종류의 Azure Storage 계정에 Event Grid 알림 구독을 설정할 수 있습니다 `BlobStorage` `StorageV2` . 
  [Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction) 사용도 지원 됩니다.
* [이벤트 허브를 만듭니다](https://docs.microsoft.com/azure/event-hubs/event-hubs-create).

### <a name="event-grid-subscription"></a>Event Grid 구독

* `Event Hub`Blob 저장소 이벤트 알림을 전송 하는 데 사용 되는 끝점 형식으로 선택 된 kusto. `Event Grid schema`알림에 대해 선택한 스키마입니다. 각 짝수 허브는 하나의 연결을 제공할 수 있습니다.
* Blob storage 구독 연결에서 형식의 알림을 처리 합니다 `Microsoft.Storage.BlobCreated` . 구독을 만들 때 선택 해야 합니다. 선택한 경우 다른 유형의 알림이 무시 됩니다.
* 한 구독은 한 컨테이너 이상에서 저장소 이벤트에 대해 알림을 받을 수 있습니다. 특정 컨테이너에서 파일을 추적 하려는 경우 다음과 같이 알림에 대 한 필터를 설정 합니다. 연결을 설정 하는 경우 다음 값을 특별히 주의 해야 합니다. 
   * **Subject는 필터로 시작** 하며 blob 컨테이너의 *리터럴* 접두사입니다. 적용된 패턴이 *startswith*이므로, 여러 컨테이너를 포함할 수 있습니다. 와일드카드는 허용되지 않습니다.
     다음과 같이 설정 *해야* 합니다 *`/blobServices/default/containers/<prefix>`* . 예: */blobServices/default/containers/StormEvents-2020-*
   * **제목 종료 문자** 필드는 Blob의 *리터럴* 접미사입니다. 와일드카드는 허용되지 않습니다. 파일 확장명 필터링에 유용 합니다.

자세한 연습은 [저장소 계정에서 Event Grid 구독을 만드는](../../../ingest-data-event-grid.md#create-an-event-grid-subscription-in-your-storage-account) 방법 가이드에서 찾을 수 있습니다.

### <a name="data-ingestion-connection-to-azure-data-explorer"></a>Azure 데이터 탐색기에 대 한 데이터 수집 연결

* Azure Portal를 통해 [Azure 데이터 탐색기에서 Event Grid 데이터 연결을 만듭니다](../../../ingest-data-event-grid.md#create-an-event-grid-data-connection-in-azure-data-explorer).
* Kusto 관리 .NET SDK 사용: [Event Grid 데이터 연결 추가](../../../data-connection-event-grid-csharp.md#add-an-event-grid-data-connection)
* Kusto management Python SDK를 사용 하 여 [Event Grid 데이터 연결 추가](../../../data-connection-event-grid-python.md#add-an-event-grid-data-connection)
* ARM 템플릿 사용: [Event Grid 데이터 연결을 추가 하기 위한 Azure Resource Manager 템플릿](../../../data-connection-event-grid-resource-manager.md#azure-resource-manager-template-for-adding-an-event-grid-data-connection)

### <a name="generating-data"></a>데이터 생성

> [!NOTE]
> * `BlockBlob`를 사용 하 여 데이터를 생성 합니다. `AppendBlob`은 지원되지 않습니다.
<!--> * Using ADLSv2 storage requires using `CreateFile` API for uploading blobs and flush at the end. 
    Kusto will get 2 notificatiopns: when blob is created and when stream is flushed. First notification arrives before the data is ready and ignored. Second notification is processed.-->

다음은 로컬 파일에서 blob을 만들고, blob 메타 데이터에 대 한 수집 속성을 설정 하 고 업로드 하는 예입니다.

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

## <a name="blob-lifecycle"></a>Blob 수명 주기

Azure 데이터 탐색기는 blob 사후 수집을 삭제 하지 않습니다. [Azure blob 저장소 수명 주기](/azure/storage/blobs/storage-lifecycle-management-concepts?tabs=azure-portal) 를 사용 하 여 blob 삭제를 관리 합니다. 3 ~ 5 일간 blob을 유지 하는 것이 좋습니다.

## <a name="known-issues"></a>알려진 문제

Azure 데이터 탐색기을 사용 하 여 event grid 수집에 사용 되는 파일을 [내보내는](../data-export/export-data-to-storage.md) 경우 다음을 참고 하세요. 
* 내보내기 명령에 제공 된 연결 문자열이 나 [외부 테이블](../data-export/export-data-to-an-external-table.md) 에 제공 된 연결 문자열이 [ADLS Gen2 형식](../../api/connection-strings/storage.md#azure-data-lake-store)(예:)의 연결 문자열이 고 `abfss://filesystem@accountname.dfs.core.windows.net` 저장소 계정에서 계층적 네임 스페이스를 사용할 수 없는 경우 Event Grid 알림이 트리거되지 않습니다. 
* 계층 구조 네임 스페이스에 대해 계정을 사용 하지 않는 경우 연결 문자열은 [Blob Storage](../../api/connection-strings/storage.md#azure-storage-blob) 형식을 사용 해야 합니다 (예: `https://accountname.blob.core.windows.net` ). 내보내기는 ADLS Gen2 연결 문자열을 사용 하는 경우에도 정상적으로 작동 하지만 알림이 트리거되지 않으며 Event Grid 수집은 작동 하지 않습니다.
