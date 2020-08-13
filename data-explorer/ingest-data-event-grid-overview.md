---
title: Event Grid 구독을 사용 하 여 저장소에서 수집-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 Event Grid 구독을 사용 하 여 저장소에서 수집 하는 방법을 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/13/2020
ms.openlocfilehash: e815a008c219c0eb1eeede8df07817d872ca1fed
ms.sourcegitcommit: f7f3ecef858c1e8d132fc10d1e240dcd209163bd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2020
ms.locfileid: "88202475"
---
# <a name="connect-to-event-grid"></a>Event Grid에 연결

Event Grid는 Azure storage를 수신 하는 파이프라인이 며 구독 된 이벤트가 발생할 때 정보를 가져오도록 Azure 데이터 탐색기를 업데이트 합니다. Azure 데이터 탐색기는 blob 생성 알림에 대 한 [Azure Event Grid](/azure/event-grid/overview) 구독과 Azure Storage (blob Storage 및 ADLSv2)에서 지속적인 수집을 제공 하 고 이벤트 허브를 통해 이러한 알림을 Azure 데이터 탐색기로 스트리밍합니다.

Event Grid 수집 파이프라인은 몇 가지 단계를 거칩니다. 대상 테이블을 만들고 Azure 데이터 탐색기에서 [특정 형식의 데이터가](#data-format) 수집 됩니다. 그런 다음 Azure 데이터 탐색기에서 Event Grid 데이터 연결을 만듭니다. Event Grid 데이터 연결은 데이터를 보낼 테이블과 테이블 매핑을 비롯 한 [이벤트 라우팅](#set-events-routing) 정보를 알고 있어야 합니다. 또한 수집 데이터, 대상 테이블 및 매핑을 설명 하는 수집 [속성도](#set-ingestion-properties)지정 합니다. 이 프로세스는 [Azure Portal](ingest-data-event-grid.md)를 통해 관리 하거나 [c #](data-connection-event-grid-csharp.md) 또는 [Python](data-connection-event-grid-python.md)을 사용 하 여 프로그래밍 방식으로 관리 하거나 [Azure Resource Manager 템플릿과](data-connection-event-grid-resource-manager.md)함께 관리할 수 있습니다.

## <a name="data-format"></a>데이터 형식

* [지원 되는 형식](ingestion-supported-formats.md)을 참조 하세요.
* [지원 되는 압축](ingestion-supported-formats.md#supported-data-compression-formats)을 참조 하세요.
  * 원래 압축 되지 않은 데이터 크기는 blob 메타 데이터의 일부 여야 합니다. 그렇지 않으면 Azure 데이터 탐색기에서 예측 합니다.  파일당 압축 되지 않은 파일 크기 제한이 4gb입니다.
 
## <a name="set-ingestion-properties"></a>수집 속성 설정

Blob 메타 데이터를 통해 blob 수집의 수집 [속성](ingestion-properties.md) 을 지정할 수 있습니다.
다음 속성을 설정할 수 있습니다.

[!INCLUDE [ingestion-properties-event-grid](includes/ingestion-properties-event-grid.md)]

## <a name="set-events-routing"></a>이벤트 라우팅 설정

Azure 데이터 탐색기 클러스터에 대 한 blob 저장소 연결을 설정할 때 대상 테이블 속성을 지정 합니다.
* 테이블 이름
* 데이터 형식
* 매핑

이 설정은 데이터에 대 한 기본 라우팅으로, 라고도 `static routing` 합니다.
Blob 메타 데이터를 사용 하 여 각 blob에 대 한 대상 테이블 속성을 지정할 수도 있습니다. 수집 [속성](#set-ingestion-properties)에 지정 된 대로 데이터가 동적으로 라우팅됩니다.

다음 예에서는 blob 메타 데이터를 업로드 하기 전에 수집 속성을 설정 하는 방법을 보여 줍니다. Blob은 다른 테이블로 라우팅됩니다.

자세한 내용은 [데이터 생성](#generate-data)을 참조 하세요.

```csharp
// Blob is dynamically routed to table `Events`, ingested using `EventsMapping` data mapping
blob = container.GetBlockBlobReference(blobName2);
blob.Metadata.Add("rawSizeBytes", "4096‬"); // the uncompressed size is 4096 bytes
blob.Metadata.Add("kustoTable", "Events");
blob.Metadata.Add("kustoDataFormat", "json");
blob.Metadata.Add("kustoIngestionMappingReference", "EventsMapping");
blob.UploadFromFile(jsonCompressedLocalFileName);
```

### <a name="generate-data"></a>데이터 생성

> [!NOTE]
> `BlockBlob`를 사용 하 여 데이터를 생성 합니다. `AppendBlob`은 지원되지 않습니다.

로컬 파일에서 blob을 만들고, 수집 속성을 blob 메타 데이터로 설정 하 고, 다음과 같이 업로드할 수 있습니다.

 ```csharp
 var azureStorageAccountConnectionString=<storage_account_connection_string>;

var containerName=<container_name>;
var blobName=<blob_name>;
var localFileName=<file_to_upload>;

// Create the container
var azureStorageAccount = CloudStorageAccount.Parse(azureStorageAccountConnectionString);
var blobClient = azureStorageAccount.CreateCloudBlobClient();
var container = blobClient.GetContainerReference(containerName);
container.CreateIfNotExists();

// Set ingestion properties in blob metadata and upload the blob
var blob = container.GetBlockBlobReference(blobName);
blob.Metadata.Add("rawSizeBytes", "4096‬"); // the uncompressed size is 4096 bytes
blob.Metadata.Add("kustoIgnoreFirstRecord", "true"); // First line of this csv file are headers
blob.UploadFromFile(csvCompressedLocalFileName);
```

> [!NOTE]
> Azure Data Lake Gen2 storage SDK를 사용 하려면 `CreateFile` 파일을 업로드 하 고 `Flush` close 매개 변수가 "true"로 설정 된 끝에를 사용 해야 합니다.
> Gen2 SDK를 올바르게 사용 하 Data Lake에 대 한 자세한 예제는 [AZURE DATA LAKE SDK를 사용 하 여 파일 업로드](data-connection-event-grid-csharp.md#upload-file-using-azure-data-lake-sdk)를 참조 하세요.

## <a name="delete-blobs-using-storage-lifecycle"></a>저장소 수명 주기를 사용 하 여 blob 삭제

Azure 데이터 탐색기은 수집 후 blob을 삭제 하지 않습니다. [Azure blob 저장소 수명 주기](/azure/storage/blobs/storage-lifecycle-management-concepts?tabs=azure-portal) 를 사용 하 여 blob 삭제를 관리 합니다. 3 ~ 5 일에 대 한 blob을 유지 하는 것이 좋습니다.

## <a name="known-event-grid-issues"></a>알려진 Event Grid 문제

* Azure 데이터 탐색기을 사용 하 여 event grid 수집에 사용 되는 파일을 [내보내는](kusto/management/data-export/export-data-to-storage.md) 경우 다음을 참고 하세요. 
    * 내보내기 명령에 제공 된 연결 문자열이 나 [외부 테이블](kusto/management/data-export/export-data-to-an-external-table.md) 에 제공 된 연결 문자열이 [ADLS Gen2 형식](kusto/api/connection-strings/storage.md#azure-data-lake-store) (예:)의 연결 문자열이 고 `abfss://filesystem@accountname.dfs.core.windows.net` 저장소 계정에서 계층적 네임 스페이스를 사용할 수 없는 경우 Event Grid 알림이 트리거되지 않습니다. 
    * 계층 구조 네임 스페이스에 대해 계정을 사용 하지 않는 경우 연결 문자열은 [Blob Storage](kusto/api/connection-strings/storage.md#azure-storage-blob) 형식을 사용 해야 합니다 (예: `https://accountname.blob.core.windows.net` ). 내보내기는 ADLS Gen2 연결 문자열을 사용 하는 경우에도 정상적으로 작동 하지만 알림이 트리거되지 않으며 Event Grid 수집은 작동 하지 않습니다.

## <a name="next-steps"></a>다음 단계

* [Event Grid 알림을 구독하여 Azure Data Explorer에 Blob 수집](ingest-data-event-grid.md)
* [C를 사용 하 여 Azure 데이터 탐색기에 대 한 이벤트 허브 데이터 연결 만들기 #](data-connection-event-hub-csharp.md)
* [Python을 사용 하 여 Azure 데이터 탐색기에 대 한 Event Grid 데이터 연결 만들기](data-connection-event-grid-python.md)
* [Azure Resource Manager 템플릿을 사용 하 여 Azure 데이터 탐색기에 대 한 Event Grid 데이터 연결 만들기](data-connection-event-grid-resource-manager.md)
