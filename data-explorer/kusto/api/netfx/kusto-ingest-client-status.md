---
title: Kusto.Ingest 참조 - 인기 상태 보고 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Kusto.Ingest 참조 - Azure 데이터 탐색기에서 수집 상태 보고에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/30/2019
ms.openlocfilehash: 1a3eed1db0ec7d3dd4bc83c0a65f342020b2a387
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81523718"
---
# <a name="kustoingest-reference---ingestion-status-reporting"></a>Kusto.Ingest 참조 - 인기 상태 보고
이 문서에서는 [IKustoQueuedIngestClient](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient) 기능을 사용하여 사용 요청 상태를 추적하는 방법을 설명합니다.

## <a name="sourcedescription-datareaderdescription-streamdescription-filedescription-and-blobdescription"></a>소스 설명, 데이터 리더 설명, 스트림 설명, 파일 설명 및 Blob설명
이러한 다양한 설명 클래스는 수집할 원본 데이터에 대한 중요한 세부 정보를 캡슐화하며 수집 작업에 제공될 수도 있고 심지어 제공되어야 합니다.
이러한 모든 클래스는 추상 `SourceDescription` 클래스에서 파생되며 각 데이터 원본에 대한 고유 식별자를 인스턴스화하는 데 사용됩니다.
제공된 식별자는 후속 작업 상태 추적을 위한 것이며 적절한 작업과 관련된 모든 보고서, 추적 및 예외에 표시됩니다.

### <a name="class-sourcedescription"></a>클래스 소스 설명
>큰 데이터 집합(예: 1GB 이상의 DataReader)을 수집할 때 데이터는 1GB 청크로 분할되고 별도로 수집됩니다.<BR>이 경우 동일한 SourceId가 동일한 데이터 집합에서 시작된 모든 인제스트 작업에 적용됩니다.   

```csharp
public abstract class SourceDescription
{
    public Guid? SourceId { get; set; }
}
```

### <a name="class-datareaderdescription"></a>클래스 데이터 리더 설명
```csharp
public class DataReaderDescription : SourceDescription
{
    public  IDataReader DataReader { get; set; }
}
```

### <a name="class-streamdescription"></a>클래스 스트림 설명
```csharp
public class StreamDescription : SourceDescription
{
    public Stream Stream { get; set; }
}
```

### <a name="class-filedescription"></a>클래스 파일 설명
```csharp
public class FileDescription : SourceDescription
{
    public string FilePath { get; set; }
}
```

### <a name="class-blobdescription"></a>클래스 Blob설명
```csharp
public class BlobDescription : SourceDescription
{
    public string BlobUri { get; set; }
    // Setting the Blob size here is important, as this saves the client the trouble of probing the blob for size
    public long? BlobSize { get; set; }
}
```

## <a name="ingestion-result-representation"></a>섭취 결과 표현

### <a name="interface-ikustoingestionresult"></a>인터페이스 IKustoingestion결과
이 인터페이스는 큐에 대기된 단일 처리 작업의 결과를 캡처하고 `SourceId`에서 검색할 수 있습니다.
```csharp
public interface IKustoIngestionResult
{
    // Retrieves the detailed ingestion status of the ingestion source with the given sourceId.
    IngestionStatus GetIngestionStatusBySourceId(Guid sourceId);

    // Retrieves the detailed ingestion status of all data ingestion operations into Kusto associated with this IKustoIngestionResult instance.
    IEnumerable<IngestionStatus> GetIngestionStatusCollection();
}
```

### <a name="class-ingestionstatus"></a>클래스 획득 상태
인기스 상태는 단일 작업의 전체 상태를 캡슐화합니다.
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
|값 |의미 |
|------------|------------|
|Pending |임시. 섭취 작업 결과에 따라 섭취 과정에서 변경될 수 있습니다. |
|성공 |영구. 그는 데이터가 성공적으로 수집되었습니다. |
|실패 |영구. 인비전에 실패한 경우 |
|Queued |영구. 수집을 위해 데이터가 큐에 대기되었습니다. |
|건너뜀 |영구. 제공된 데이터가 없고 수집 작업이 건너뜁니다. |
|부분적으로 성공 |영구. 일부 데이터가 실패한 동안 데이터의 일부가 성공적으로 수집되었습니다. |

## <a name="tracking-ingestion-status-kustoqueuedingestclient"></a>인기 상태 추적(KustoQueuedingestClient)
[IKustoQueuedIngestClient는](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient) '불-앤-잊어버린' 클라이언트- 클라이언트 측의 인기 작업은 클라이언트 작업이 완료된 후 Azure 큐에 메시지를 게시하여 종료됩니다. 클라이언트 사용자의 편의를 위해 KustoQueuedIngestClient는 개별 사용 상태를 추적하는 메커니즘을 제공합니다. 이는 처리량이 많은 수집 파이프라인에서 대량 사용을 위한 것이 아니라 속도가 상대적으로 낮고 추적 요구 사항이 매우 엄격할 때 '정밀도' 수집을 위한 것입니다.

> [!WARNING]
> 대용량 데이터 스트림에 대한 모든 수집 요청에 대해 긍정적인 알림을 켜는 것은 기본 xStore 리소스에 극단적인 부하를 주므로 > 수집 대기 시간이 증가하고 클러스터 응답성이 완전해질 수 있습니다.


다음 [속성(KustoQueuedingestionProperties에](kusto-ingest-client-reference.md#class-kustoqueuedingestionproperties)설정)은 인레이션 성공 또는 실패 알림에 대한 수준 및 전송을 제어합니다.

### <a name="ingestionreportlevel-enumeration"></a>인제션리포트레벨 열거
```csharp
public enum IngestionReportLevel
{
    FailuresOnly = 0,
    None,
    FailuresAndSuccesses
}
```

### <a name="ingestionreportmethod-enumeration"></a>인제션보고서메서드 열거
```csharp
public enum IngestionReportMethod
{
    Queue = 0,
    Table,
    QueueAndTable
}
```

인기 상태를 추적하려면 다음을 사용하여 인제스트 작업을 수행하는 [IKustoQueuedIngestClient에](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient) 다음을 제공해야 합니다.
1.  보고서의 `IngestionReportLevel`원하는 수준으로 속성을 설정 - 실패만 (기본값입니다) 또는 FailuresAnd성공.
없음으로 설정하면 인베이션이 끝날 때 아무 것도 보고되지 않습니다.
2.  원하는 `IngestionReportMethod` - 큐, 테이블 또는 둘 다 지정합니다.

사용 예제는 [Kusto.Ingest 예제](kusto-ingest-client-examples.md) 페이지에서 찾을 수 있습니다.

### <a name="ingestion-status-in-azure-table"></a>Azure 테이블의 섭취 상태
각 `IKustoIngestionResult` 인제스트 작업에서 반환되는 인터페이스에는 인기 의 상태를 쿼리하는 데 사용할 수 있는 함수가 포함되어 있습니다.
반환된 `IngestionStatus` 개체의 `Status` 속성에 특히 주의하십시오.
* `Pending`소스가 인베이션을 위해 큐에 대기중이며 아직 업데이트되지 않은 것을 나타냅니다. 함수를 사용하여 소스 상태를 쿼리합니다.
* `Succeeded`소스가 성공적으로 인제되었음을 나타냅니다.
* `Failed`원본을 인제스트하지 못했음을 나타냅니다.

>`Queued` 상태를 가져오는 것은 'Queue'의 기본값에 남아 `IngestionReportMethod` 있음을 나타냅니다. 이것은 영구적 인 상태이며 GetIngestionStatusId 또는 GetIngestionStatusCollection 함수를 다시 호출하면 항상 동일한 '큐에 대기' 상태가 됩니다.<BR>Azure Table에서 인젝션 상태를 확인하려면 `IngestionReportMethod` [KustoQueuedingestionProperties의](kusto-ingest-client-reference.md#class-kustoqueuedingestionproperties) 속성이 설정되어 `Table` 있는지(또는 `QueueAndTable` 큐에 보고할 수 있는 경우)를 섭취하기 전에 확인하십시오.

### <a name="ingestion-status-in-azure-queue"></a>Azure 큐의 인고 상태
메서드는 `IKustoIngestionResult` Azure Table에서 상태를 확인하는 데만 관련이 있습니다. Azure 큐에 보고된 상태를 쿼리하려면 [IKustoQueuedingestClient의](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient)다음 메서드를 사용합니다.

|방법 |목적 |
|------------|------------|
|픽탑링실패 |요청된 메시지 제한에 따라 삭제되지 않은 초기 수집 실패에 대한 정보를 반환하는 비동기 메서드 |
|GetandDiscardTopingestion실패 |요청된 메시지 제한에 따라 삭제되지 않은 초기 사용 실패를 반환하고 삭제하는 비동기 메서드 |
|GetAnd폐기탑화성공 |요청된 메시지 제한에 따라 삭제되지 않은 초기 사용 성공 사례를 반환하고 폐기하는 비동기 메서드( `IngestionReportLevel``FailuresAndSuccesses` |


### <a name="ingestion-failures-retrieved-from-azure-queue"></a>Azure 큐에서 검색된 인비전에실패
수집 실패는 오류와 관련된 `IngestionFailure` 유용한 정보를 포함하는 개체로 표시됩니다.

|속성 |의미 |
|------------|------------|
|데이터베이스 & 테이블 |의도한 데이터베이스 및 테이블 이름 |
|인제션소스패스 |섭취한 Blob의 경로입니다. 파일의 섭취시 원본 파일 이름이 포함됩니다. 데이터 리더의 섭취의 경우 임의로 됩니다. |
|오류 상태 |`Permanent`(재시도가 실행되지 않습니다), `Transient` (재시도가 실행됩니다) 또는 `Exhausted` (여러 번의 재시도 실패) |
|작업ID& 루트액티드 |작업 ID 및 사용의 루트 활동 ID(추가 문제 해결에 유용) |
|실패 |실패의 UTC 시간입니다. 수집을 실행하기 전에 데이터가 집계되기 때문에 수집 메서드가 호출된 시간보다 클 수 있습니다. |
|세부 정보 |오류와 관련된 기타 세부 정보(있는 경우) |
|ErrorCode |`IngestionErrorCode`오류가 발생할 경우 를 대비하여 인비온 오류 코드를 나타내는 열거형|