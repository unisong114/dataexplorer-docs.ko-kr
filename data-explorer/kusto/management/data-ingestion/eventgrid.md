---
title: 이벤트 그리드 구독을 사용하여 저장소에서 수집 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 이벤트 그리드 구독을 사용하여 저장소에서 인제스트에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/01/2020
ms.openlocfilehash: 682c39b11292c7e198dba46dad9b5bfa3b520c0f
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81521525"
---
# <a name="ingest-from-storage-using-event-grid-subscription"></a>이벤트 그리드 구독을 사용하여 스토리지에서 인제스트

Azure Data Explorer는 Blob 만든 알림에 대한 [Azure Event Grid](https://docs.microsoft.com/azure/event-grid/overview) 구독을 사용하여 Azure 저장소(Blob 저장소 및 ADLSv2)에서 지속적인 수집을 제공하고 이벤트 허브를 통해 이러한 알림을 Kusto로 스트리밍합니다.

## <a name="data-format"></a>데이터 형식

* Blob은 [지원되는 형식중](https://docs.microsoft.com/azure/data-explorer/ingestion-supported-formats)어느 한 쪽일 수 있습니다.
* Blob은 [지원되는 압축에서](https://docs.microsoft.com/azure/data-explorer/ingestion-supported-formats#supported-data-compression-formats) 압축할 수 있습니다.

> [!NOTE]
> 원래 압축되지 않은 데이터 크기는 Blob 메타데이터의 일부여야 합니다.
> 압축되지 않은 크기를 지정하지 않으면 Azure Data 탐색기는 파일 크기에 따라 추정합니다. Blob 메타데이터의 `rawSizeBytes` [속성을](#ingestion-properties) **압축되지 않은** 데이터 크기로 바이트로 설정하여 원래 데이터 크기를 제공합니다.
> 4GB 의 파일당 압축되지 않은 크기 제한이 있습니다.

## <a name="ingestion-properties"></a>수집 속성

Blob 메타데이터를 통해 Blob 수집의 [인기 속성을](https://docs.microsoft.com/azure/data-explorer/ingestion-properties) 지정할 수 있습니다.
다음 속성을 설정할 수 있습니다.

|속성 | Description|
|---|---|
| rawSizeBytes | 원시(압축되지 않은) 데이터의 크기입니다. Avro/ORC/마루의 경우 형식별 압축이 적용되기 전의 크기입니다.|
| 쿠스토 테이블 |  기존 대상 테이블의 이름입니다. 블레이드의 `Table` 집합을 `Data Connection` 재정의합니다. |
| 쿠스토데이터형식 |  데이터 형식입니다. 블레이드의 `Data format` 집합을 `Data Connection` 재정의합니다. |
| 쿠스토인션스매핑레퍼런스 |  사용할 기존 인베이션 매핑의 이름입니다. 블레이드의 `Column mapping` 집합을 `Data Connection` 재정의합니다.|
| 쿠스토무시퍼스트레코드 | `true`로 설정하면 Azure 데이터 탐색기는 Blob의 첫 번째 행을 무시합니다. 테이블 형식 데이터(CSV, TSV 또는 이와 유사한)에서 헤더를 무시합니다. |
| 쿠스토익스택 | 결과 익스텐트에 첨부될 태그를 나타내는 [문자열입니다.](../extents-overview.md#extent-tagging) |
| 쿠스토크리에이션타임 |  ISO 8601 문자열로 서식이 지정된 Blob에 대해 [$IngestionTime](../../query/ingestiontimefunction.md?pivots=azuredataexplorer) 재정의합니다. 백필에 사용합니다. |

## <a name="events-routing"></a>이벤트 라우팅

Azure Data Explorer 클러스터에 Blob 저장소 연결을 설정할 때 대상 테이블 속성(테이블 이름, 데이터 형식 및 매핑)을 지정합니다. 이 라우팅은 데이터에 대한 기본 라우팅이며. `static routig`
Blob 메타데이터를 사용하여 각 Blob에 대한 대상 테이블 속성을 지정할 수도 있습니다. 데이터는 [수집 속성에](#ingestion-properties)의해 지정된 대로 동적으로 라우팅됩니다.

다음은 수집 속성을 업로드하기 전에 Blob 메타데이터에 설정하는 예제입니다. Blob은 다른 테이블로 라우팅됩니다.

데이터를 생성하는 방법에 대한 자세한 내용은 [샘플 코드를](#generating-data) 참조하십시오.

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
> 최상의 성능을 위해 Azure 데이터 탐색기 클러스터와 동일한 리전의 모든 리소스를 만듭니다.

### <a name="prerequisites"></a>사전 요구 사항

* [스토리지 계정 만들기](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) 
  이벤트 그리드 알림 구독은 Azure 저장소 `BlobStorage` 계정의 종류 또는 `StorageV2`에서 설정할 수 있습니다. 
  데이터 [레이크 스토리지 Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction) 를 사용하도록 설정도 지원됩니다.
* [이벤트 허브를 만듭니다.](https://docs.microsoft.com/azure/event-hubs/event-hubs-create)

### <a name="event-grid-subscription"></a>Event Grid 구독

* Kusto는 `Event Hub` Blob 저장소 이벤트 알림을 전송하는 데 사용되는 endpoind 유형으로 선택되었습니다. `Event Grid schema`는 알림에 대해 선택한 스키마입니다. 각 Even 허브는 하나의 연결을 제공할 수 있습니다.
* Blob 저장소 구독 연결은 형식의 `Microsoft.Storage.BlobCreated`알림을 처리합니다. 구독을 만들 때 선택해야 합니다. 다른 유형의 알림이 선택된 경우 무시됩니다.
* 하나의 구독은 하나의 컨테이너 이상의 저장소 이벤트에 대해 알릴 수 있습니다. 특정 컨테이너에서 파일을 추적하려면 다음과 같이 알림에 대한 필터를 설정합니다. 
   * **필터로 시작하는 제목은** Blob 컨테이너의 *리터럴* 접두사입니다. 적용된 패턴이 *startswith*이므로, 여러 컨테이너를 포함할 수 있습니다. 와일드카드는 허용되지 않습니다.
     다음과 같이 *설정해야 합니다.* *`/blobServices/default/containers/<prefix>`* 예: */blobServices/기본/컨테이너/StormEvents-2020-*
   * **제목 종료 문자** 필드는 Blob의 *리터럴* 접미사입니다. 와일드카드는 허용되지 않습니다. 파일 확장자를 필터링하는 데 유용합니다.

자세한 설명은 저장소 계정 가이드의 [이벤트 그리드 구독 만들기 방법에서](https://docs.microsoft.com/azure/data-explorer/ingest-data-event-grid#create-an-event-grid-subscription-in-your-storage-account) 확인할 수 있습니다.

### <a name="data-ingestion-connection-to-azure-data-explorer"></a>Azure 데이터 탐색기의 데이터 수집 연결

* Azure 포털을 통해: [Azure 데이터 탐색기에서 이벤트 그리드 데이터 연결을 만듭니다.](https://docs.microsoft.com/azure/data-explorer/ingest-data-event-grid#create-an-event-grid-data-connection-in-azure-data-explorer)
* Kusto 관리 .NET SDK 사용: [이벤트 그리드 데이터 연결 추가](https://docs.microsoft.com/azure/data-explorer/data-connection-event-grid-csharp#add-an-event-grid-data-connection)
* Kusto 관리 파이썬 SDK 사용: [이벤트 그리드 데이터 연결 추가](https://docs.microsoft.com/azure/data-explorer/data-connection-event-grid-python#add-an-event-grid-data-connection)
* ARM 템플릿 사용: [이벤트 그리드 데이터 연결을 추가하기 위한 Azure 리소스 관리자 템플릿](https://docs.microsoft.com/azure/data-explorer/data-connection-event-grid-resource-manager#azure-resource-manager-template-for-adding-an-event-grid-data-connection)

### <a name="generating-data"></a>데이터 생성

> [!NOTE]
> * 데이터를 `BlockBlob` 생성하는 데 사용합니다. `AppendBlob`은 지원되지 않습니다.
<!--> * Using ADLSv2 storage requires using `CreateFile` API for uploading blobs and flush at the end. 
    Kusto will get 2 notificatiopns: when blob is created and when stream is flushed. First notification arrives before the data is ready and ignored. Second notification is processed.-->

다음은 로컬 파일에서 Blob을 만들고, 수집 속성을 Blob 메타데이터에 설정하고 업로드하는 예제입니다.

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

Azure 데이터 탐색기는 수집 후 Blob을 삭제하지 않지만 3~5일 동안 유지됩니다. [Azure Blob 저장소 수명 주기를](https://docs.microsoft.com/azure/storage/blobs/storage-lifecycle-management-concepts?tabs=azure-portal) 사용하여 Blob 삭제를 관리합니다.