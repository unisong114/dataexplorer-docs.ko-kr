---
title: 진단 로그를 사용 하 여 Azure 데이터 탐색기 수집, 명령 및 쿼리 모니터링
description: 수집 명령 및 쿼리 작업을 모니터링 하기 위해 Azure 데이터 탐색기에 대 한 진단 로그를 설정 하는 방법에 대해 알아봅니다.
author: orspod
ms.author: orspodek
ms.reviewer: guregini
ms.service: data-explorer
ms.topic: how-to
ms.date: 09/16/2020
ms.openlocfilehash: fed4027d946792448f2c564d8daa019c991b50d2
ms.sourcegitcommit: 3af95ea6a6746441ac71b1a217bbb02ee23d5f28
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/23/2020
ms.locfileid: "95473586"
---
# <a name="monitor-azure-data-explorer-ingestion-commands-and-queries-using-diagnostic-logs"></a>진단 로그를 사용 하 여 Azure 데이터 탐색기 수집, 명령 및 쿼리 모니터링

Azure Data Explorer는 애플리케이션, 웹 사이트, IoT 디바이스 등으로부터 대량의 데이터 스트리밍에 대한 실시간 분석을 제공하는 빠른 속도의 완전 관리형 데이터 분석 서비스입니다. [Azure Monitor 진단 로그](/azure/azure-monitor/platform/diagnostic-logs-overview) 는 Azure 리소스의 작동에 대 한 데이터를 제공 합니다. Azure 데이터 탐색기는 수집 성공, 수집 실패, 명령 및 쿼리 작업에 대 한 정보를 얻기 위해 진단 로그를 사용 합니다. 작업 로그를 Azure Storage, 이벤트 허브 또는 Log Analytics로 내보내 수집, 명령 및 쿼리 상태를 모니터링할 수 있습니다. 추가 분석을 위해 Azure Storage 및 Azure Event Hub의 로그를 Azure 데이터 탐색기 클러스터의 테이블로 라우팅할 수 있습니다.

> [!IMPORTANT] 
> 진단 로그 데이터에는 중요 한 데이터가 포함 될 수 있습니다. 모니터링 요구 사항에 따라 로그 대상의 사용 권한을 제한 합니다. 

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독이 없는 경우 [무료 azure 계정](https://azure.microsoft.com/free/)을 만듭니다.
* [Azure Portal](https://portal.azure.com/)에 로그인합니다.
* [클러스터 및 데이터베이스](create-cluster-database-portal.md)를 만듭니다.

## <a name="set-up-diagnostic-logs-for-an-azure-data-explorer-cluster"></a>Azure 데이터 탐색기 클러스터에 대 한 진단 로그 설정

진단 로그를 사용 하 여 다음 로그 데이터의 컬렉션을 구성할 수 있습니다.

# <a name="ingestion"></a>[수집](#tab/ingestion)

> [!NOTE]
> 수집 로그는 Sdk, 데이터 연결 및 커넥터를 사용 하 여 수집 끝점에 대 한 대기 중인 수집을 지원 합니다.
>
> 수집 로그는 스트리밍 수집, 엔진으로 직접 수집, 쿼리에서 수집 또는 설정 또는-추가 명령에 대해 지원 되지 않습니다.

* 수집 **작업 성공**: 이러한 로그에 수집 작업을 성공적으로 완료 하는 방법에 대 한 정보가 있습니다.
* **실패** 한 수집 작업: 이러한 로그는 오류 정보를 포함 하 여 실패 한 수집 작업에 대 한 자세한 정보를 포함 합니다. 
* 수집 **일괄 처리 작업**: 이러한 로그에는 수집할 준비가 된 일괄 처리에 대 한 자세한 통계 (기간, 일괄 처리 크기 및 blob 수)가 있습니다.

# <a name="commands-and-queries"></a>[명령 및 쿼리](#tab/commands-and-queries)

* **명령**: 이러한 로그는 최종 상태에 도달한 관리자 명령에 대 한 정보를 포함 합니다.
* **쿼리**: 이러한 로그는 최종 상태에 도달한 쿼리에 대 한 자세한 정보를 포함 합니다. 

    > [!NOTE]
    > 쿼리 로그 데이터에는 쿼리 텍스트가 포함 되지 않습니다.

---

그런 다음 데이터는 사용자의 사양에 따라 저장소 계정에 보관, 이벤트 허브로 스트리밍 또는 Log Analytics으로 전송 됩니다.

### <a name="enable-diagnostic-logs"></a>진단 로그 활성화

진단 로그는 기본적으로 해제되어 있습니다. 진단 로그를 사용 하도록 설정 하려면 다음 단계를 수행 합니다.

1. [Azure Portal](https://portal.azure.com)에서 모니터링할 Azure 데이터 탐색기 클러스터 리소스를 선택 합니다.
1. **모니터링** 아래에서 **진단 설정** 을 선택합니다.
  
    ![진단 로그 추가](media/using-diagnostic-logs/add-diagnostic-logs.png)

1. **진단 설정 추가** 를 선택합니다.
1. **진단 설정** 창에서 다음을 수행 합니다.

    :::image type="content" source="media/using-diagnostic-logs/configure-diagnostics-settings.png" alt-text="진단 설정 구성":::

    1. **진단 설정 이름을** 입력 합니다.
    1. 하나 이상의 대상 (Log Analytics 작업 영역, 저장소 계정 또는 이벤트 허브)을 선택 합니다.
    1. 수집할 로그를 선택 합니다.,, `SucceededIngestion` `FailedIngestion` `Command` , 또는 `Query` , `TableUsageStatistics` 또는 `TableDetails` 입니다.
    1. 수집할 [메트릭](using-metrics.md#supported-azure-data-explorer-metrics) (선택 사항)을 선택 합니다.  
    1. **저장** 을 선택 하 여 새 진단 로그 설정 및 메트릭을 저장 합니다.

몇 분 안에 새 설정이 설정 됩니다. 그러면 로그는 구성 된 보관 대상 (저장소 계정, 이벤트 허브 또는 Log Analytics)에 표시 됩니다. 

> [!NOTE]
> Log Analytics로 로그를 전송 하는 경우,, `SucceededIngestion` `FailedIngestion` `Command` 및 `Query` 로그는 각각,,, `SucceededIngestion` `FailedIngestion` `ADXIngestionBatching` `ADXCommand` , `ADXQuery` , 이라는 Log Analytics 테이블에 저장 됩니다.

## <a name="diagnostic-logs-schema"></a>진단 로그 스키마

모든 [Azure Monitor 진단 로그는 일반적인 최상위 스키마를 공유](/azure/azure-monitor/platform/diagnostic-logs-schema)합니다. Azure 데이터 탐색기에는 고유한 이벤트에 대 한 고유한 속성이 있습니다. 모든 로그는 JSON 형식으로 저장 됩니다.

# <a name="ingestion"></a>[수집](#tab/ingestion)

### <a name="ingestion-logs-schema"></a>수집 로그 스키마

로그 JSON 문자열에는 다음 표에 나열 된 요소가 포함 됩니다.

|속성               |설명
|---                |---
|time               |보고서의 시간
|resourceId         |Azure Resource Manager 리소스 ID
|operationName      |작업 이름: ' MICROSOFT. KUSTO/클러스터/수집/작업 '
|operationVersion   |스키마 버전: ' 1.0 ' 
|category           |작업의 범주입니다. `SucceededIngestion`, `FailedIngestion` 또는 `IngestionBatching`. [작업 성공](#successful-ingestion-operation-log), [실패 한 작업](#failed-ingestion-operation-log) 또는 [일괄 처리 작업](#ingestion-batching-operation-log)에 대 한 속성이 다릅니다.
|properties         |작업에 대 한 자세한 정보입니다.

#### <a name="successful-ingestion-operation-log"></a>수집 작업 로그가 성공 했습니다.

**예:**

```json
{
    "time": "",
    "resourceId": "",
    "operationName": "MICROSOFT.KUSTO/CLUSTERS/INGEST/ACTION",
    "operationVersion": "1.0",
    "category": "SucceededIngestion",
    "properties":
    {
        "SucceededOn": "2019-05-27 07:55:05.3693628",
        "OperationId": "b446c48f-6e2f-4884-b723-92eb6dc99cc9",
        "Database": "Samples",
        "Table": "StormEvents",
        "IngestionSourceId": "66a2959e-80de-4952-975d-b65072fc571d",
        "IngestionSourcePath": "https://kustoingestionlogs.blob.core.windows.net/sampledata/events8347293.json",
        "RootActivityId": "d0bd5dd3-c564-4647-953e-05670e22a81d"
    }
}
```
**성공한 작업 진단 로그의 속성**

|속성               |설명
|---                |---
|SucceededOn        |수집 완료 시간
|OperationId        |Azure 데이터 탐색기 수집 작업 ID
|데이터베이스           |대상 데이터베이스의 이름입니다.
|테이블              |대상 테이블의 이름입니다.
|IngestionSourceId  |수집 데이터 원본의 ID
|IngestionSourcePath|수집 데이터 원본 또는 blob URI의 경로입니다.
|RootActivityId     |활동 ID

#### <a name="failed-ingestion-operation-log"></a>수집 작업 로그가 실패 했습니다.

**예:**

```json
{
    "time": "",
    "resourceId": "",
    "operationName": "MICROSOFT.KUSTO/CLUSTERS/INGEST/ACTION",
    "operationVersion": "1.0",
    "category": "FailedIngestion",
    "properties":
    {
        "failedOn": "2019-05-27 08:57:05.4273524",
        "operationId": "5956515d-9a48-4544-a514-cf4656fe7f95",
        "database": "Samples",
        "table": "StormEvents",
        "ingestionSourceId": "eee56f8c-2211-4ea4-93a6-be556e853e5f",
        "ingestionSourcePath": "https://kustoingestionlogs.blob.core.windows.net/sampledata/events5725592.json",
        "rootActivityId": "52134905-947a-4231-afaf-13d9b7b184d5",
        "details": "Permanent failure downloading blob. URI: ..., permanentReason: Download_SourceNotFound, DownloadFailedException: 'Could not find file ...'",
        "errorCode": "Download_SourceNotFound",
        "failureStatus": "Permanent",
        "originatesFromUpdatePolicy": false,
        "shouldRetry": false
    }
}
```

**실패 한 작업 진단 로그의 속성**

|속성               |설명
|---                |---
|FailedOn           |수집 완료 시간
|OperationId        |Azure 데이터 탐색기 수집 작업 ID
|데이터베이스           |대상 데이터베이스의 이름입니다.
|테이블              |대상 테이블의 이름입니다.
|IngestionSourceId  |수집 데이터 원본의 ID
|IngestionSourcePath|수집 데이터 원본 또는 blob URI의 경로입니다.
|RootActivityId     |활동 ID
|세부 정보            |오류 및 오류 메시지에 대 한 자세한 설명
|오류 코드          |오류 코드 
|FailureStatus      |`Permanent` 또는 `Transient` 일시적인 오류를 다시 시도 하면 성공할 수 있습니다.
|OriginatesFromUpdatePolicy|업데이트가 업데이트 정책에서 발생 하는 경우 True입니다.
|ShouldRetry        |다시 시도 하는 경우 True

#### <a name="ingestion-batching-operation-log"></a>수집 일괄 처리 작업 로그

**예:**

```json
{
  "resourceId": "/SUBSCRIPTIONS/12534EB3-8109-4D84-83AD-576C0D5E1D06/RESOURCEGROUPS/KEREN/PROVIDERS/MICROSOFT.KUSTO/CLUSTERS/KERENEUS",
  "time": "2020-05-27T07:55:05.3693628Z",
  "operationVersion": "1.0",
  "operationName": "MICROSOFT.KUSTO/CLUSTERS/INGESTIONBATCHING/ACTION",
  "category": "IngestionBatching",
  "correlationId": "2bb51038-c7dc-4ebd-9d7f-b34ece4cb735",
  "properties": {
    "Database": "Samples",
    "Table": "StormEvents",
    "BatchingType": "Size",
    "SourceCreationTime": "2020-05-27 07:52:04.9623640",
    "BatchTimeSeconds": 215.5,
    "BatchSizeBytes": 2356425,
    "DataSourcesInBatch": 4,
    "RootActivityId": "2bb51038-c7dc-4ebd-9d7f-b34ece4cb735"
  }
}

```
**수집 일괄 처리 작업 진단 로그의 속성**

|속성               |Description
|---                   |---
| TimeGenerated        | 이 이벤트가 생성 된 시간 (UTC)입니다. |
| 데이터베이스             | 대상 테이블을 보유 하는 데이터베이스의 이름입니다. |
| 테이블                | 데이터가 수집 대상 테이블의 이름입니다. |
| BatchingType         | 일괄 처리 유형: 일괄 처리에서 일괄 처리 시간, 데이터 크기 또는 일괄 처리 정책에 의해 설정 된 파일 수 제한에 도달 했는지 여부 |
| SourceCreationTime   | 이 일괄 처리의 blob이 생성 된 최소 시간 (UTC) |
| BatchTimeSeconds     | 이 일괄 처리의 총 일괄 처리 시간 (초) |
| BatchSizeBytes       | 이 일괄 처리에서 데이터의 압축 되지 않은 총 크기 (바이트) |
| DataSourcesInBatch   | 이 일괄 처리의 데이터 원본 수 |
| RootActivityId       | 작업의 작업 ID |


# <a name="commands-and-queries"></a>[명령 및 쿼리](#tab/commands-and-queries)

### <a name="commands-and-queries-logs-schema"></a>명령 및 쿼리 로그 스키마

로그 JSON 문자열에는 다음 표에 나열 된 요소가 포함 됩니다.

|속성               |설명
|---                |---
|time               |보고서의 시간
|resourceId         |Azure Resource Manager 리소스 ID
|operationName      |작업 이름: ' MICROSOFT. KUSTO/클러스터/명령/작업 ' 또는 "MICROSOFT. KUSTO/클러스터/쿼리/동작 ". 명령 및 쿼리 로그에 대 한 속성은 다릅니다.
|operationVersion   |스키마 버전: ' 1.0 ' 
|category           |작업의 범주 `Command` 입니다. `Query` 명령 및 쿼리 로그에 대 한 속성은 다릅니다.
|properties         |작업에 대 한 자세한 정보입니다.

#### <a name="command-log"></a>명령 로그

**예:**

```json
{
    "time": "",
    "resourceId": "",
    "operationName": "MICROSOFT.KUSTO/CLUSTERS/COMMAND/ACTION",
    "operationVersion": "1.0",
    "category": "Command",
    "properties":
    {
        "RootActivityId": "d0bd5dd3-c564-4647-953e-05670e22a81d",
        "StartedOn": "2020-09-12T18:06:04.6898353Z",
        "LastUpdatedOn": "2020-09-12T18:06:04.6898353Z",
        "Database": "DatabaseSample",
        "State": "Completed",
        "FailureReason": "XXX",
        "TotalCpu": "00:01:30.1562500",
        "CommandType": "ExtentsDrop",
        "Application": "Kusto.WinSvc.DM.Svc",
        "ResourceUtilization": "{\"CacheStatistics\":{\"Memory\":{\"Hits\":0,\"Misses\":0},\"Disk\":{\"Hits\":0,\"Misses\":0},\"Shards\":{\"Hot\":{\"HitBytes\":0,\"MissBytes\":0,\"RetrieveBytes\":0},\"Cold\":{\"HitBytes\":0,\"MissBytes\":0,\"RetrieveBytes\":0},\"BypassBytes\":0}},\"TotalCpu\":\"00:00:00\",\"MemoryPeak\":0,\"ScannedExtentsStatistics\":{\"MinDataScannedTime\":null,\"MaxDataScannedTime\":null,\"TotalExtentsCount\":0,\"ScannedExtentsCount\":0,\"TotalRowsCount\":0,\"ScannedRowsCount\":0}}",
        "Duration": "00:03:30.1562500",
        "User": "AAD app id=0571b364-eeeb-4f28-ba74-90a8b4132b53",
        "Principal": "aadapp=0571b364-eeeb-4f28-ba74-90a3b4136b53;5c443533-c927-4410-a5d6-4d6a5443b64f"
    }
}
```
**명령 진단 로그의 속성**

|속성               |설명
|---                |---
|RootActivityId |루트 작업 ID
|StartedOn        |이 명령이 시작 된 시간 (UTC)
|LastUpdatedOn        |이 명령이 종료 된 시간 (UTC)
|데이터베이스          |명령이 실행 된 데이터베이스의 이름입니다.
|주              |명령이 종료 된 상태입니다.
|FailureReason  |실패 이유
|TotalCpu |총 CPU 기간
|CommandType     |명령 유형
|애플리케이션     |명령을 호출한 응용 프로그램 이름
|ResourceUtilization     |명령 리소스 사용률
|기간     |명령 기간
|사용자     |쿼리를 호출한 사용자입니다.
|주 서버     |쿼리를 호출한 보안 주체입니다.

#### <a name="query-log"></a>쿼리 로그

**예:**

```json
{
    "time": "",
    "resourceId": "",
    "operationName": "MICROSOFT.KUSTO/CLUSTERS/QUERY/ACTION",
    "operationVersion": "1.0",
    "category": "Query",
    "properties": {
        "RootActivityId": "3e6e8814-e64f-455a-926d-bf16229f6d2d",
        "StartedOn": "2020-09-04T13:45:45.331925Z",
        "LastUpdatedOn": "2020-09-04T13:45:45.3484372Z",
        "Database": "DatabaseSample",
        "State": "Completed",
        "FailureReason": "[none]",
        "TotalCpu": "00:00:00",
        "ApplicationName": "MyApp",
        "MemoryPeak": 0,
        "Duration": "00:00:00.0165122",
        "User": "AAD app id=0571b364-eeeb-4f28-ba74-90a8b4132b53",
        "Principal": "aadapp=0571b364-eeeb-4f28-ba74-90a8b4132b53;5c823e4d-c927-4010-a2d8-6dda2449b6cf",
        "ScannedExtentsStatistics": {
            "MinDataScannedTime": "2020-07-27T08:34:35.3299941",
            "MaxDataScannedTime": "2020-07-27T08:34:41.991661",
            "TotalExtentsCount": 64,
            "ScannedExtentsCount": 64,
            "TotalRowsCount": 320,
            "ScannedRowsCount": 320
        },
        "CacheStatistics": {
            "Memory": {
                "Hits": 192,
                "Misses": 0
          },
            "Disk": {
                "Hits": 0,
                "Misses": 0
      },
            "Shards": {
                "Hot": {
                    "HitBytes": 0,
                    "MissBytes": 0,
                    "RetrieveBytes": 0
        },
                "Cold": {
                    "HitBytes": 0,
                    "MissBytes": 0,
                    "RetrieveBytes": 0
        },
            "BypassBytes": 0
      }
    },
    "ResultSetStatistics": {
        "TableCount": 1,
        "TablesStatistics": [
        {
          "RowCount": 1,
          "TableSize": 9
        }
      ]
    }
  }
}
```

**쿼리 진단 로그의 속성**

|속성               |설명
|---                |---
|RootActivityId |루트 작업 ID
|StartedOn        |이 명령이 시작 된 시간 (UTC)
|LastUpdatedOn           |이 명령이 종료 된 시간 (UTC)
|데이터베이스              |명령이 실행 된 데이터베이스의 이름입니다.
|주  |명령이 종료 된 상태입니다.
|FailureReason|실패 이유
|TotalCpu     |총 CPU 기간
|ApplicationName            |쿼리를 호출한 응용 프로그램 이름
|MemoryPeak          |최대 메모리
|기간      |명령 기간
|사용자|쿼리를 호출한 사용자입니다.
|주 서버        |쿼리를 호출한 보안 주체입니다.
|ScannedExtentsStatistics        | 검색 된 익스텐트 통계 포함
|Min\canned 시간        |최소 데이터 검색 시간
|Maxldcanned 시간        |최대 데이터 검색 시간
|TotalExtentsCount        |총 익스텐트 수
|ScannedExtentsCount        |검색 된 익스텐트 수
|TotalRowsCount        |총 행 수
|ScannedRowsCount        |검색 된 행 수
|CacheStatistics        |캐시 통계 포함
|메모리        |캐시 메모리 통계를 포함 합니다.
|적중 횟수        |메모리 캐시 적중
|누락        |메모리 캐시 누락
|디스크        |캐시 디스크 통계 포함
|적중 횟수        |디스크 캐시 적중
|누락        |디스크 캐시 누락
|분할        |핫 및 콜드 분할 cache 통계 포함
|핫        |핫 분할 cache 통계 포함
|HitBytes        |분할 hot cache 적중
|오 Sbytes        |분할 hot cache 누락
|RetrieveBytes        | 분할 hot cache 검색 바이트
|콜드        |콜드 분할 cache 통계 포함
|HitBytes        |분할 콜드 캐시 적중 횟수
|오 Sbytes        |분할 콜드 캐시 누락
|RetrieveBytes        |분할 콜드 캐시 검색 바이트
|BypassBytes        |분할 cache 무시 바이트
|ResultSetStatistics        |결과 집합 통계를 포함 합니다.
|TableCount        |결과 집합 테이블 수
|표 통계        |결과 집합 테이블 통계를 포함 합니다.
|RowCount        | 결과 집합 테이블 행 개수
|TableSize        |결과 집합 테이블 행 개수

---

## <a name="next-steps"></a>다음 단계

* [메트릭을 사용하여 클러스터 상태 모니터링](using-metrics.md)
* [자습서: 수집 진단 로그를 수집 하 고 Azure 데이터 탐색기에서 모니터링 데이터 수집 및 쿼리](ingest-data-no-code.md)
