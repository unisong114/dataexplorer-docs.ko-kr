---
title: Kusto. 수집 상태 보고-Azure 데이터 탐색기
description: 이 문서에서는 Kusto. Azure 데이터 탐색기에서 수집 상태 보고를 수집 하는 방법을 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/30/2019
ms.openlocfilehash: 41c6f7868d5300dc6a7234ee774ad8c78b08c36e
ms.sourcegitcommit: fd3bf300811243fc6ae47a309e24027d50f67d7e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/13/2020
ms.locfileid: "83382185"
---
# <a name="kustoingest-ingestion-status-reporting"></a>Kusto 수집 수집 상태 보고

이 문서에서는 [IKustoQueuedIngestClient](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient) 기능을 사용 하 여 수집 요청의 상태를 추적 하는 방법을 설명 합니다.

## <a name="description-classes"></a>설명 클래스

이러한 설명 클래스는 수집 될 원본 데이터에 대 한 중요 한 세부 정보를 포함 하며 수집 작업에 사용 해야 합니다. 

* SourceDescription
* DataReaderDescription
* StreamDescription
* FileDescription
* BlobDescription

클래스는 모두 추상 클래스에서 파생 `SourceDescription` 되며, 각 데이터 소스에 대 한 고유 식별자를 인스턴스화하는 데 사용 됩니다. 그러면 각 식별자가 상태 추적에 사용 되 고 관련 작업과 관련 된 모든 보고서, 추적 및 예외에 표시 됩니다.

### <a name="class-sourcedescription"></a>클래스 원본 설명

대량 데이터 집합은 1GB 청크로 분할 되 고 각 부분은 별도로 수집 됩니다. 동일한 SourceId는 동일한 데이터 집합에서 시작 된 모든 수집 작업에 적용 됩니다.   

```csharp
public abstract class SourceDescription
{
    public Guid? SourceId { get; set; }
}
```

### <a name="class-datareaderdescription"></a>클래스 DataReaderDescription

```csharp
public class DataReaderDescription : SourceDescription
{
    public  IDataReader DataReader { get; set; }
}
```

### <a name="class-streamdescription"></a>클래스 StreamDescription

```csharp
public class StreamDescription : SourceDescription
{
    public Stream Stream { get; set; }
}
```

### <a name="class-filedescription"></a>클래스 FileDescription

```csharp
public class FileDescription : SourceDescription
{
    public string FilePath { get; set; }
}
```

### <a name="class-blobdescription"></a>클래스 BlobDescription

```csharp
public class BlobDescription : SourceDescription
{
    public string BlobUri { get; set; }
    // Setting the Blob size here is important, as this saves the client the trouble of probing the blob for size
    public long? BlobSize { get; set; }
}
```

## <a name="ingestion-result-representation"></a>수집 결과 표현

### <a name="interface-ikustoingestionresult"></a>인터페이스 IKustoIngestionResult

이 인터페이스는 대기 중인 단일 수집 작업의 결과를 캡처하여에서 검색할 수 있습니다 `SourceId` .

```csharp
public interface IKustoIngestionResult
{
    // Retrieves the detailed ingestion status of the ingestion source with the given sourceId.
    IngestionStatus GetIngestionStatusBySourceId(Guid sourceId);

    // Retrieves the detailed ingestion status of all data ingestion operations into Kusto associated with this IKustoIngestionResult instance.
    IEnumerable<IngestionStatus> GetIngestionStatusCollection();
}
```

### <a name="class-ingestionstatus"></a>클래스 IngestionStatus

IngestionStatus는 단일 수집 작업의 전체 상태를 포함 합니다.

```csharp
public class IngestionStatus
{
    // The ingestion status returns from the service. Status remains 'Pending' during the ingestion process and
    // is updated by the service once the ingestion completes. When <see cref="IngestionReportMethod"/> is set to 'Queue' the ingestion status
    // will always be 'Queued' and the caller needs to query the report queues for ingestion status, as configured. To query statuses that were
    // reported to queue, see: <see href="https://docs.microsoft.com/azure/kusto/api/netfx/kusto-ingest-client-status#ingestion-status-in-azure-queue"/>.
    // When <see cref="IngestionReportMethod"/> is set to 'Table', call <see cref="IKustoIngestionResult.GetIngestionStatusBySourceId"/> or
    // <see cref="IKustoIngestionResult.GetIngestionStatusCollection"/> to retrieve the most recent ingestion status.
    public Status Status { get; set; }
    // A unique identifier representing the ingested source. Can be supplied during the ingestion execution.
    public Guid IngestionSourceId { get; set; }
    // The URI of the blob, potentially including the secret needed to access the blob.
    // This can be a filesystem URI (on-premises deployments only), or an Azure Blob Storage URI (including a SAS key or a semicolon followed by the account key)
    public string IngestionSourcePath { get; set; }
    // The name of the database holding the target table.
    public string Database { get; set; }
    // The name of the target table into which the data will be ingested.
    public string Table { get; set; }
    // The last updated time of the ingestion status.
    public DateTime UpdatedOn { get; set; }
    // The ingestion's operation Id.
    public Guid OperationId { get; set; }
    // The ingestion operation activity Id.
    public Guid ActivityId { get; set; }
    // In case of a failure - indicates the failure's error code.
    public IngestionErrorCode ErrorCode { get; set; }
    // In case of a failure - indicates the failure's status.
    public FailureStatus FailureStatus { get; set; }
    // In case of a failure - indicates the failure's details.
    public string Details { get; set; }
    // In case of a failure - indicates whether or not the failures originate from an Update Policy.
    public bool OriginatesFromUpdatePolicy { get; set; }
}
```

### <a name="status-enumeration"></a>상태 열거

|값              |의미                                                                                     |임시/영구
|-------------------|-----------------------------------------------------------------------------------------------------|---------|
|Pending            |수집 작업의 결과에 따라 수집 중에 값이 변경 될 수 있습니다. |임시|
|성공          |데이터가 성공적으로 수집.                                                              |영구| 
|Failed             |수집 실패                                                                                     |영구|
|Queued             |수집을 위해 데이터를 큐에 넣었습니다.                                                               |영구|
|건너뜀            |제공 된 데이터가 없고 수집 작업을 건너뛰었습니다.                                            |영구|
|PartiallySucceeded |데이터의 일부가 성공적으로 수집 일부 실패 했습니다.                                        |영구|

## <a name="tracking-ingestion-status-kustoqueuedingestclient"></a>수집 상태 추적 (KustoQueuedIngestClient)

[IKustoQueuedIngestClient](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient) 는 ' 화재 및 잊은 ' 클라이언트입니다. 클라이언트 쪽의 수집 작업은 Azure 큐에 메시지를 게시 하 여 종료 됩니다. 게시 후 클라이언트 작업이 완료 됩니다. 클라이언트 사용자 편의를 위해 KustoQueuedIngestClient는 개별 수집 상태를 추적 하는 메커니즘을 제공 합니다. 이 메커니즘은 높은 처리량 수집 파이프라인에서 대량 사용 하기 위한 것이 아닙니다. 이 메커니즘은 속도가 상대적으로 낮고 추적 요구 사항이 엄격한 경우 정밀도 수집에 적합 합니다.

> [!WARNING]
> 대량 볼륨 데이터 스트림에 대 한 모든 수집 요청에 대해 긍정 알림을 설정 하는 것은 사용 하지 않는 것이 좋습니다 .이는 기본 xStore 리소스를 로드 하는 데 상당한 부하가 발생 하기 때문입니다 .이로 인해 수집 대기 시간 및 완전 한 클러스터 응답성이 증가할 수 있습니다.

다음 속성 ( [KustoQueuedIngestionProperties](kusto-ingest-client-reference.md#class-kustoqueuedingestionproperties)에 설정)은 수집 성공 또는 실패 알림에 대 한 수준 및 전송을 제어 합니다.

### <a name="ingestionreportlevel-enumeration"></a>IngestionReportLevel 열거형

```csharp
public enum IngestionReportLevel
{
    FailuresOnly = 0,
    None,
    FailuresAndSuccesses
}
```

### <a name="ingestionreportmethod-enumeration"></a>IngestionReportMethod 열거형

```csharp
public enum IngestionReportMethod
{
    Queue = 0,
    Table,
    QueueAndTable
}
```

수집 상태를 추적 하려면 수집 작업을 수행 하는 [IKustoQueuedIngestClient](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient) 에 다음을 제공 합니다.
1.  `IngestionReportLevel`속성을 필요한 보고서 수준으로 설정 합니다. `FailuresOnly`(기본값) 또는 `FailuresAndSuccesses` 입니다.
로 설정 되 면 수집 `None` 종료 시 아무것도 보고 되지 않습니다.
1.  `IngestionReportMethod`  -  `Queue` , 또는를 지정 `Table` `both` 합니다.

사용 예제는 [Kusto. 수집 예제](kusto-ingest-client-examples.md) 페이지에서 찾을 수 있습니다.

### <a name="ingestion-status-in-the-azure-table"></a>Azure 테이블의 수집 상태

`IKustoIngestionResult`각 수집 작업에서 반환 되는 인터페이스에는 수집 상태를 쿼리 하는 데 사용할 수 있는 함수가 포함 되어 있습니다.
`Status`반환 된 개체의 속성에 특히 주의 해야 합니다 `IngestionStatus` .
* `Pending`는 수집을 위해 소스가 대기 되었으며 아직 업데이트 되지 않았음을 나타냅니다. 함수를 다시 사용 하 여 원본의 상태를 쿼리 합니다.
* `Succeeded`소스가 성공적으로 수집 되었음을 나타냅니다.
* `Failed`소스가 수집 하지 못했음을 나타냅니다.

> [!NOTE]
> 상태 가져오기는 `Queued` `IngestionReportMethod` 가 ' Queue '의 기본값에 남아 있음을 나타냅니다. 영구적인 상태 이며 또는 함수를 다시 호출 하면 `GetIngestionStatusBySourceId` `GetIngestionStatusCollection` 항상 동일한 ' 대기 ' 상태가 됩니다.
> 수집 이전에 Azure 테이블에서 수집 상태를 확인 하려면 `IngestionReportMethod` [KustoQueuedIngestionProperties](kusto-ingest-client-reference.md#class-kustoqueuedingestionproperties) 의 속성이로 설정 되어 있는지 확인 `Table` 합니다. 또한 수집 상태를 큐에 보고 하려면 상태를로 설정 `QueueAndTable` 합니다.

### <a name="ingestion-status-in-azure-queue"></a>Azure 큐의 수집 상태

`IKustoIngestionResult`메서드는 Azure 테이블의 상태를 확인 하는 경우에만 해당 됩니다. Azure 큐에 보고 된 상태를 쿼리하려면 다음 [IKustoQueuedIngestClient](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient) 메서드를 사용 합니다.

|메서드                                  |목적     |
|----------------------------------------|------------|
|PeekTopIngestionFailures                |요청 된 메시지에 대 한 제한으로 인해 아직 삭제 되지 않은 가장 이른 수집 오류에 대 한 정보를 반환 하는 비동기 메서드입니다. |
|GetAndDiscardTopIngestionFailures       |요청 된 메시지에 대 한 제한으로 인해 아직 삭제 되지 않은 가장 이른 수집 오류를 반환 하 고 삭제 하는 비동기 메서드입니다. |
|GetAndDiscardTopIngestionSuccesses      |요청 된 메시지에 대 한 제한으로 인해 아직 삭제 되지 않은 가장 이른 수집 성공을 반환 하 고 삭제 하는 비동기 메서드입니다. 이 메서드는 `IngestionReportLevel` 가로 설정 된 경우에만 해당 됩니다.`FailuresAndSuccesses` |

### <a name="ingestion-failures-retrieved-from-the-azure-queue"></a>Azure 큐에서 수집 오류가 검색 되었습니다.

수집 오류는 `IngestionFailure` 오류에 대 한 유용한 정보를 포함 하는 개체로 표현 됩니다.

|속성                      |의미     |
|------------------------------|------------|
|데이터베이스 & 테이블              |원하는 데이터베이스 및 테이블 이름 |
|IngestionSourcePath           |수집 blob의 경로입니다. 파일이 수집 인 경우 원래 파일 이름을 포함 합니다. DataReader가 수집 인 경우 임의가 됩니다. |
|FailureStatus                 |`Permanent`(다시 시도는 실행 되지 않음), `Transient` (다시 시도는 실행 됨) 또는 `Exhausted` (여러 번의 재시도도 실패 함) |
|OperationId & RootActivityId id  |수집의 작업 ID 및 RootActivity ID (추가 문제 해결에 유용) |
|FailedOn                      |실패 한 UTC 시간입니다. 수집을 실행 하기 전에 데이터를 집계 하기 때문에 수집 메서드가 호출 된 시간 보다 커집니다. |
|세부 정보                       |실패와 관련 된 기타 세부 정보 (있는 경우) |
|오류 코드                     |`IngestionErrorCode`열거형은 오류가 발생 한 경우 수집 오류 코드를 나타냅니다. |
