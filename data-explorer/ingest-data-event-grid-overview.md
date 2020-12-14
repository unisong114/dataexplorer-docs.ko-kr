---
title: Event Grid 구독을 사용 하 여 저장소에서 수집-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 Event Grid 구독을 사용 하 여 저장소에서 수집 하는 방법을 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: how-to
ms.date: 08/13/2020
ms.openlocfilehash: fde0e79fbe8a8080fa6e21dde12434de5c92353f
ms.sourcegitcommit: d9e203a54b048030eeb6d05b01a65902ebe4e0b8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/14/2020
ms.locfileid: "97371461"
---
# <a name="event-grid-data-connection"></a>Event Grid 데이터 연결

Event Grid 수집은 Azure storage를 수신 하는 파이프라인이 며 등록 된 이벤트가 발생할 때 정보를 가져오도록 Azure 데이터 탐색기를 업데이트 합니다. Azure 데이터 탐색기는 blob 생성 또는 blob 이름 변경 알림을 위한 [Azure Event Grid](/azure/event-grid/overview) 구독과 함께 Azure Storage (blob Storage 및 ADLSv2)에서 지속적인 수집을 제공 하 고 이벤트 허브를 통해 이러한 알림을 Azure 데이터 탐색기로 스트리밍합니다.

Event Grid 수집 파이프라인은 몇 가지 단계를 거칩니다. Azure 데이터 탐색기에서 [특정 형식의 데이터가](#data-format) 수집는 대상 테이블을 만듭니다. 그런 다음 Azure 데이터 탐색기에서 Event Grid 데이터 연결을 만듭니다. Event Grid 데이터 연결은 데이터를 보낼 테이블과 테이블 매핑을 비롯 한 [이벤트 라우팅](#events-routing) 정보를 알고 있어야 합니다. 또한 수집 데이터, 대상 테이블 및 매핑을 설명 하는 수집 [속성도](#ingestion-properties)지정 합니다. 샘플 데이터를 생성 하 고 [blob을 업로드](#upload-blobs) 하 여 연결을 테스트할 수 있습니다. 수집 후 [blob을 삭제](#delete-blobs-using-storage-lifecycle) 합니다. 이 프로세스는 [한 번 클릭](one-click-ingestion-new-table.md)수집, [c #](data-connection-event-grid-csharp.md) 또는 [Python](data-connection-event-grid-python.md)을 사용 하 여 프로그래밍 방식으로 또는 [Azure Resource Manager 템플릿을](data-connection-event-grid-resource-manager.md)사용 하 여 [Azure Portal](ingest-data-event-grid.md)를 통해 관리할 수 있습니다. 

Azure 데이터 탐색기에서 데이터를 수집 하는 방법에 대 한 일반적인 내용은 [azure 데이터 탐색기 데이터 수집 개요](ingest-data-overview.md)를 참조 하세요.

## <a name="data-format"></a>데이터 형식

* [지원 되는 형식](ingestion-supported-formats.md)을 참조 하세요.
* [지원 되는 압축](ingestion-supported-formats.md#supported-data-compression-formats)을 참조 하세요.
    * 원래 압축 되지 않은 데이터 크기는 blob 메타 데이터의 일부 여야 합니다. 그렇지 않으면 Azure 데이터 탐색기에서 예측 합니다. 파일당 압축 되지 않은 파일 크기 제한이 4gb입니다.

> [!NOTE]
> `BlobStorage`, `StorageV2` 또는 [Data Lake Storage Gen2](/azure/storage/blobs/data-lake-storage-introduction)에 대 한 Azure Storage 계정에 Event Grid 알림 구독을 설정할 수 있습니다.

## <a name="ingestion-properties"></a>수집 속성

Blob 메타 데이터를 통해 blob 수집의 수집 [속성](ingestion-properties.md) 을 지정할 수 있습니다.
다음 속성을 설정할 수 있습니다.

[!INCLUDE [ingestion-properties-event-grid](includes/ingestion-properties-event-grid.md)]

## <a name="events-routing"></a>이벤트 라우팅

Azure 데이터 탐색기 클러스터에 대 한 blob 저장소 연결을 설정할 때 대상 테이블 속성을 지정 합니다.
* 테이블 이름
* 데이터 형식
* 매핑

이 설정은 데이터에 대 한 기본 라우팅으로, 라고도 `static routing` 합니다.
Blob 메타 데이터를 사용 하 여 각 blob에 대 한 대상 테이블 속성을 지정할 수도 있습니다. 수집 [속성](#ingestion-properties)에 지정 된 대로 데이터가 동적으로 라우팅됩니다.

다음 예에서는 blob 메타 데이터를 업로드 하기 전에 수집 속성을 설정 하는 방법을 보여 줍니다. Blob은 다른 테이블로 라우팅됩니다.

자세한 내용은 [blob 업로드](#upload-blobs)를 참조 하세요.

```csharp
// Blob is dynamically routed to table `Events`, ingested using `EventsMapping` data mapping
blob = container.GetBlockBlobReference(blobName2);
blob.Metadata.Add("rawSizeBytes", "4096‬"); // the uncompressed size is 4096 bytes
blob.Metadata.Add("kustoTable", "Events");
blob.Metadata.Add("kustoDataFormat", "json");
blob.Metadata.Add("kustoIngestionMappingReference", "EventsMapping");
blob.UploadFromFile(jsonCompressedLocalFileName);
```

## <a name="upload-blobs"></a>Blob 업로드

로컬 파일에서 blob을 만들고 수집 속성을 blob 메타 데이터로 설정 하 고 업로드할 수 있습니다. 예제는 [Event Grid 알림을 구독 하 여 Azure 데이터 탐색기에 Blob 수집](ingest-data-event-grid.md#generate-sample-data) 을 참조 하세요.

> [!NOTE]
> * `BlockBlob`를 사용 하 여 데이터를 생성 합니다. `AppendBlob`은 지원되지 않습니다.
> * Azure Data Lake Gen2 storage SDK를 사용 하려면 `CreateFile` 파일을 업로드 하 고 `Flush` close 매개 변수가 "true"로 설정 된 끝에를 사용 해야 합니다.
> Gen2 SDK를 올바르게 사용 하 Data Lake에 대 한 자세한 예제는 [AZURE DATA LAKE SDK를 사용 하 여 파일 업로드](data-connection-event-grid-csharp.md#upload-file-using-azure-data-lake-sdk)를 참조 하세요.
> * 이벤트 허브 끝점이 이벤트 수신을 승인 하지 않으면 Azure Event Grid는 재시도 메커니즘을 활성화 합니다. 이 재시도 배달이 실패 하면 배달 *못* 한 편지 처리를 사용 하 여 배달 되지 않은 이벤트를 저장소 계정으로 배달할 수 Event Grid. 자세한 내용은 [Event Grid 메시지 배달 및 재시도](/azure/event-grid/delivery-and-retry#retry-schedule-and-duration)를 참조하세요.

## <a name="delete-blobs-using-storage-lifecycle"></a>저장소 수명 주기를 사용 하 여 blob 삭제

Azure 데이터 탐색기은 수집 후 blob을 삭제 하지 않습니다. [Azure blob 저장소 수명 주기](/azure/storage/blobs/storage-lifecycle-management-concepts?tabs=azure-portal) 를 사용 하 여 blob 삭제를 관리 합니다. 3 ~ 5 일에 대 한 blob을 유지 하는 것이 좋습니다.

## <a name="known-event-grid-issues"></a>알려진 Event Grid 문제

* Azure 데이터 탐색기을 사용 하 여 event grid 수집에 사용 되는 파일을 [내보내는](kusto/management/data-export/export-data-to-storage.md) 경우 다음을 참고 하세요. 
    * 내보내기 명령에 제공 된 연결 문자열이 나 [외부 테이블](kusto/management/data-export/export-data-to-an-external-table.md) 에 제공 된 연결 문자열이 [ADLS Gen2 형식](kusto/api/connection-strings/storage.md#azure-data-lake-store) (예:)의 연결 문자열이 고 `abfss://filesystem@accountname.dfs.core.windows.net` 저장소 계정에서 계층적 네임 스페이스를 사용할 수 없는 경우 Event Grid 알림이 트리거되지 않습니다.
    * 계층 구조 네임 스페이스에 대해 계정을 사용 하지 않는 경우 연결 문자열은 [Blob Storage](kusto/api/connection-strings/storage.md#azure-storage-blob) 형식을 사용 해야 합니다 (예: `https://accountname.blob.core.windows.net` ). 내보내기는 ADLS Gen2 연결 문자열을 사용 하는 경우에도 정상적으로 작동 하지만 알림이 트리거되지 않으며 Event Grid 수집은 작동 하지 않습니다.

## <a name="next-steps"></a>다음 단계

* [Event Grid 알림을 구독하여 Azure Data Explorer에 Blob 수집](ingest-data-event-grid.md)
* [C를 사용 하 여 Azure 데이터 탐색기에 대 한 Event Grid 데이터 연결 만들기 #](data-connection-event-grid-csharp.md)
* [Python을 사용 하 여 Azure 데이터 탐색기에 대 한 Event Grid 데이터 연결 만들기](data-connection-event-grid-python.md)
* [Azure Resource Manager 템플릿을 사용 하 여 Azure 데이터 탐색기에 대 한 Event Grid 데이터 연결 만들기](data-connection-event-grid-resource-manager.md)
