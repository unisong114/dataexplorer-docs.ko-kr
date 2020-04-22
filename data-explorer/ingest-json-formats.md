---
title: Azure 데이터 탐색기로 JSON 서식을 지정한 데이터 수집
description: JSON 서식이 지정된 데이터를 Azure 데이터 탐색기로 통합하는 방법에 대해 알아봅니다.
author: orspod
ms.author: orspodek
ms.reviewer: kerend
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/27/2020
ms.openlocfilehash: 36c7616ee3d79cebaea96ca67787466cbd401cab
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663175"
---
# <a name="ingest-json-formatted-sample-data-into-azure-data-explorer"></a>Azure 데이터 탐색기로 JSON 서식을 지정한 샘플 데이터 수집

이 문서에서는 JSON 서식이 지정된 데이터를 Azure 데이터 탐색기 데이터베이스에 통합하는 방법을 보여 주며 있습니다. 원시 및 매핑된 JSON의 간단한 예제로 시작하여 여러 줄지어 있는 JSON을 계속 한 다음 배열과 사전을 포함하는 보다 복잡한 JSON 스키마를 해결합니다.  이 예제에서는 Kusto 쿼리 언어(KQL), C#또는 파이썬을 사용하여 JSON 형식의 데이터를 수집하는 프로세스를 자세히 설명합니다. Kusto 쿼리 `ingest` 언어 제어 명령은 엔진 끝점에 직접 실행됩니다. 프로덕션 시나리오에서 수집은 클라이언트 라이브러리 또는 데이터 연결을 사용하여 데이터 관리 서비스에 실행됩니다. [Azure 데이터 탐색기 파이썬 라이브러리를 사용하여 인제스트 데이터](/azure/data-explorer/python-ingest-data) 읽기 및 Azure 데이터 [탐색기 .NET 표준 SDK를 사용하여 데이터 수집을 사용하여](/azure/data-explorer/net-standard-ingest-data) 이러한 클라이언트 라이브러리를 사용하여 데이터 수집에 대한 워크스루를 확인할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

[테스트 클러스터 및 데이터베이스](create-cluster-database-portal.md)

## <a name="the-json-format"></a>JSON 형식

Azure 데이터 탐색기는 두 가지 JSON 파일 형식을 지원합니다.
* `json`: 라인 분리 JSON. 입력 데이터의 각 줄에는 정확히 하나의 JSON 레코드가 있습니다.
* `multijson`: 멀티 안감 JSON. 파서는 줄 구분 기호를 무시하고 이전 위치에서 유효한 JSON의 끝까지 레코드를 읽습니다.

### <a name="ingest-and-map-json-formatted-data"></a>JSON 형식의 데이터 수집 및 매핑

JSON 서식이 지정된 데이터를 수집하려면 [수집 속성을](ingestion-properties.md)사용하여 *형식을* 지정해야 합니다. JSON 데이터를 수집하려면 JSON 원본 항목을 대상 열에 매핑하는 [매핑이](kusto/management/mappings.md)필요합니다. 데이터를 수집할 때 미리 정의된 `jsonMappingReference` 수집 속성을 사용하거나 `jsonMapping`수집 속성을 지정합니다. 이 문서에서는 `jsonMappingReference` 인기 사용에 사용되는 테이블에 미리 정의된 인기 속성입니다. 아래 예제에서는 JSON 레코드를 단일 열 테이블에 원시 데이터로 인데스트로 수집하는 것으로 시작합니다. 그런 다음 매핑을 사용하여 매핑된 열에 각 속성을 가져옵니다. 

### <a name="simple-json-example"></a>간단한 JSON 예제

다음 예제는 평평한 구조의 간단한 JSON입니다. 데이터는 여러 장치에 의해 수집 된 온도 및 습도 정보를 가지고 있습니다. 각 레코드에는 ID와 타임스탬프가 표시됩니다.

```json
{
    "timestamp": "2019-05-02 15:23:50.0369439",
    "deviceId": "2945c8aa-f13e-4c48-4473-b81440bb5ca2",
    "messageId": "7f316225-839a-4593-92b5-1812949279b3",
    "temperature": 31.0301639051317,
    "humidity": 62.0791099602725
}
```

## <a name="ingest-raw-json-records"></a>인제스트 원시 JSON 레코드 

이 예제에서는 JSON 레코드를 단일 열 테이블에 원시 데이터로 수집합니다. 쿼리를 사용하여 데이터 조작, 업데이트 정책은 데이터를 수집한 후에 수행됩니다.

# <a name="kql"></a>[KQL](#tab/kusto-query-language)

Kusto 쿼리 언어를 사용하여 원시 JSON 형식으로 데이터를 수집합니다.

1. [https://dataexplorer.azure.com](https://dataexplorer.azure.com)에 로그인합니다.

1. **클러스터 추가**를 선택합니다.

1. **클러스터 추가** 대화 상자에 `https://<ClusterName>.<Region>.kusto.windows.net/` 형식으로 클러스터 URL을 입력하고 **추가**를 선택합니다.

1. 다음 명령에 붙여넣은 다음 **실행을** 선택하여 테이블을 만듭니다.

    ```kusto
    .create table RawEvents (Event: dynamic)
    ```

    이 쿼리는 [동적](kusto/query/scalar-data-types/dynamic.md) 데이터 `Event` 형식의 단일 열이 있는 테이블을 만듭니다.

1. JSON 매핑을 만듭니다.

    ```kusto
    .create table RawEvents ingestion json mapping 'RawEventMapping' '[{"column":"Event","path":"$"}]'
    ```

    이 명령은 매핑을 만들고 JSON 루트 `$` 경로를 `Event` 열에 매핑합니다.

1. 테이블에 데이터를 수집합니다. `RawEvents`

    ```kusto
    .ingest into table RawEvents h'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/simple.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D' with (format=json, jsonMappingReference=RawEventMapping)
    ```

# <a name="c"></a>[C#](#tab/c-sharp)

C#을 사용하여 원시 JSON 형식으로 데이터를 수집합니다.

1. `RawEvents` 테이블을 만듭니다.

    ```csharp
    var kustoUri = "https://<ClusterName>.<Region>.kusto.windows.net:443/";
    var kustoConnectionStringBuilder =
        new KustoConnectionStringBuilder(ingestUri)
        {
            FederatedSecurity = true,
            InitialCatalog = database,
            UserID = user,
            Password = password,
            Authority = tenantId
        };
    var kustoClient = KustoClientFactory.CreateCslAdminProvider(kustoConnectionStringBuilder);

    var table = "RawEvents";
    var command =
        CslCommandGenerator.GenerateTableCreateCommand(
            table,
            new[]
            {
                Tuple.Create("Events", "System.Object"),
            });

    kustoClient.ExecuteControlCommand(command);
    ```

1. JSON 매핑을 만듭니다.
    
    ```csharp
    var tableMapping = "RawEventMapping";
    var command =
        CslCommandGenerator.GenerateTableJsonMappingCreateCommand(
            tableName,
            tableMapping,
            new[]
            {
                new JsonColumnMapping {ColumnName = "Events", JsonPath = "$"},
            });

    kustoClient.ExecuteControlCommand(command);
    ```
    이 명령은 매핑을 만들고 JSON 루트 `$` 경로를 `Event` 열에 매핑합니다.

1. 테이블에 데이터를 수집합니다. `RawEvents`

    ```csharp
    var ingestUri = "https://ingest-<ClusterName>.<Region>.kusto.windows.net:443/";
    var blobPath = "https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/simple.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D";
    var ingestConnectionStringBuilder =
        new KustoConnectionStringBuilder(ingestUri)
        {
            FederatedSecurity = true,
            InitialCatalog = database,
            UserID = user,
            Password = password,
            Authority = tenantId
        };
    var ingestClient = KustoIngestFactory.CreateQueuedIngestClient(ingestConnectionStringBuilder);
    
    var properties =
        new KustoQueuedIngestionProperties(database, table)
        {
            Format = DataSourceFormat.json,
            IngestionMappingReference = tableMapping
        };

    ingestClient.IngestFromSingleBlob(blobPath, deleteSourceOnSuccess: false, ingestionProperties: properties);
    ```

> [!NOTE]
> 데이터는 일괄 처리 [정책에](kusto/management/batchingpolicy.md)따라 집계되어 몇 분의 대기 시간이 발생합니다.

# <a name="python"></a>[Python](#tab/python)

파이썬을 사용하여 원시 JSON 형식으로 데이터를 수집합니다.

1. `RawEvents` 테이블을 만듭니다.

    ```python
    KUSTO_URI = "https://<ClusterName>.<Region>.kusto.windows.net:443/"
    KCSB_DATA = KustoConnectionStringBuilder.with_aad_device_authentication(KUSTO_URI, AAD_TENANT_ID)
    KUSTO_CLIENT = KustoClient(KCSB_DATA)
    TABLE = "RawEvents"

    CREATE_TABLE_COMMAND = ".create table " + TABLE + " (Events: dynamic)"
    RESPONSE = KUSTO_CLIENT.execute_mgmt(DATABASE, CREATE_TABLE_COMMAND)
    dataframe_from_result_table(RESPONSE.primary_results[0])
    ```

1. JSON 매핑을 만듭니다.

    ```python
    MAPPING = "RawEventMapping"
    CREATE_MAPPING_COMMAND = ".create table " + TABLE + " ingestion json mapping '" + MAPPING + """' '[{"column":"Event","path":"$"}]'"""
    RESPONSE = KUSTO_CLIENT.execute_mgmt(DATABASE, CREATE_MAPPING_COMMAND)
    dataframe_from_result_table(RESPONSE.primary_results[0])
    ```

1. 테이블에 데이터를 수집합니다. `RawEvents`

    ```python
    INGEST_URI = "https://ingest-<ClusterName>.<Region>.kusto.windows.net:443/"
    KCSB_INGEST = KustoConnectionStringBuilder.with_aad_device_authentication(INGEST_URI, AAD_TENANT_ID)
    INGESTION_CLIENT = KustoIngestClient(KCSB_INGEST)
    BLOB_PATH = 'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/simple.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D'
    
    INGESTION_PROPERTIES = IngestionProperties(database=DATABASE, table=TABLE, dataFormat=DataFormat.json, mappingReference=MAPPING)
    BLOB_DESCRIPTOR = BlobDescriptor(BLOB_PATH, FILE_SIZE)
    INGESTION_CLIENT.ingest_from_blob(
        BLOB_DESCRIPTOR, ingestion_properties=INGESTION_PROPERTIES)
    ```

    > [!NOTE]
    > 데이터는 일괄 처리 [정책에](kusto/management/batchingpolicy.md)따라 집계되어 몇 분의 대기 시간이 발생합니다.

---

## <a name="ingest-mapped-json-records"></a>인제스트 매핑 JSON 레코드

이 예제에서는 JSON 레코드 데이터를 수집합니다. 각 JSON 속성은 테이블의 단일 열에 매핑됩니다. 

# <a name="kql"></a>[KQL](#tab/kusto-query-language)

1. JSON 입력 데이터와 유사한 스키마를 사용하여 새 테이블을 만듭니다. 다음 예제 및 인제스트 명령에 이 테이블을 사용합니다. 

    ```kusto
    .create table Events (Time: datetime, Device: string, MessageId: string, Temperature: double, Humidity: double)
    ```

1. JSON 매핑을 만듭니다.

    ```kusto
    .create table Events ingestion json mapping 'FlatEventMapping' '[{"column":"Time","path":"$.timestamp"},{"column":"Device","path":"$.deviceId"},{"column":"MessageId","path":"$.messageId"},{"column":"Temperature","path":"$.temperature"},{"column":"Humidity","path":"$.humidity"}]'
    ```

    이 매핑에서 테이블 스키마에 의해 정의된 `timestamp` 대로 항목은 열에 `Time` 데이터 `datetime` 형식으로 수집됩니다.

1. 테이블에 데이터를 수집합니다. `Events`

    ```kusto
    .ingest into table Events h'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/simple.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D' with (format=json, jsonMappingReference=FlatEventMapping)
    ```

    파일 'simple.json'에는 몇 줄로 구분된 JSON 레코드가 있습니다. 형식은 `json`은이며 인제스트 명령에 사용되는 매핑은 생성한 `FlatEventMapping` 매핑입니다.

# <a name="c"></a>[C#](#tab/c-sharp)

1. JSON 입력 데이터와 유사한 스키마를 사용하여 새 테이블을 만듭니다. 다음 예제 및 인제스트 명령에 이 테이블을 사용합니다. 

    ```csharp
    var table = "Events";
    var command =
        CslCommandGenerator.GenerateTableCreateCommand(
            table,
            new[]
            {
                Tuple.Create("Time", "System.DateTime"),
                Tuple.Create("Device", "System.String"),
                Tuple.Create("MessageId", "System.String"),
                Tuple.Create("Temperature", "System.Double"),
                Tuple.Create("Humidity", "System.Double"),
            });

    kustoClient.ExecuteControlCommand(command);
    ```

1. JSON 매핑을 만듭니다.

    ```csharp
    var tableMapping = "FlatEventMapping";
    var command =
        CslCommandGenerator.GenerateTableJsonMappingCreateCommand(
            tableName,
            tableMapping,
            new[]
            {
                        new JsonColumnMapping {ColumnName = "Time", JsonPath = "$.timestamp"},
                        new JsonColumnMapping {ColumnName = "Device", JsonPath = "$.deviceId"},
                        new JsonColumnMapping {ColumnName = "MessageId", JsonPath = "$.messageId"},
                        new JsonColumnMapping {ColumnName = "Temperature", JsonPath = "$.temperature"},
                        new JsonColumnMapping {ColumnName = "Humidity", JsonPath = "$.humidity"},
            });

    kustoClient.ExecuteControlCommand(command);
    ```

    이 매핑에서 테이블 스키마에 의해 정의된 `timestamp` 대로 항목은 열에 `Time` 데이터 `datetime` 형식으로 수집됩니다.    

1. 테이블에 데이터를 수집합니다. `Events`

    ```csharp
    var blobPath = "https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/simple.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D";
    var properties =
        new KustoQueuedIngestionProperties(database, table)
        {
            Format = DataSourceFormat.json,
            IngestionMappingReference = tableMapping
        };

    ingestClient.IngestFromSingleBlob(blobPath, deleteSourceOnSuccess: false, ingestionProperties: properties);
    ```

    파일 'simple.json'에는 몇 줄로 구분된 JSON 레코드가 있습니다. 형식은 `json`은이며 인제스트 명령에 사용되는 매핑은 생성한 `FlatEventMapping` 매핑입니다.

# <a name="python"></a>[Python](#tab/python)

1. JSON 입력 데이터와 유사한 스키마를 사용하여 새 테이블을 만듭니다. 다음 예제 및 인제스트 명령에 이 테이블을 사용합니다. 

    ```python
    TABLE = "RawEvents"
    CREATE_TABLE_COMMAND = ".create table " + TABLE + " (Time: datetime, Device: string, MessageId: string, Temperature: double, Humidity: double)"
    RESPONSE = KUSTO_CLIENT.execute_mgmt(DATABASE, CREATE_TABLE_COMMAND)
    dataframe_from_result_table(RESPONSE.primary_results[0])
    ```

1. JSON 매핑을 만듭니다.

    ```python
    MAPPING = "FlatEventMapping"
    CREATE_MAPPING_COMMAND = ".create table Events ingestion json mapping '" + MAPPING + """' '[{"column":"Time","path":"$.timestamp"},{"column":"Device","path":"$.deviceId"},{"column":"MessageId","path":"$.messageId"},{"column":"Temperature","path":"$.temperature"},{"column":"Humidity","path":"$.humidity"}]'""" 
    RESPONSE = KUSTO_CLIENT.execute_mgmt(DATABASE, CREATE_MAPPING_COMMAND)
    dataframe_from_result_table(RESPONSE.primary_results[0])
    ```

1. 테이블에 데이터를 수집합니다. `Events`

    ```python
    BLOB_PATH = 'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/simple.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D'
    
    INGESTION_PROPERTIES = IngestionProperties(database=DATABASE, table=TABLE, dataFormat=DataFormat.json, mappingReference=MAPPING)
    BLOB_DESCRIPTOR = BlobDescriptor(BLOB_PATH, FILE_SIZE)
    INGESTION_CLIENT.ingest_from_blob(
        BLOB_DESCRIPTOR, ingestion_properties=INGESTION_PROPERTIES)
    ```

    파일 'simple.json'에는 JSON 레코드가 몇 줄 로 구분되어 있습니다. 형식은 `json`은이며 인제스트 명령에 사용되는 매핑은 생성한 `FlatEventMapping` 매핑입니다.    
---

## <a name="ingest-multi-lined-json-records"></a>인제스트 멀티 라이닝 JSON 레코드

이 예제에서는 다중 줄 지어 JSON 레코드를 섭취합니다. 각 JSON 속성은 테이블의 단일 열에 매핑됩니다. 파일 'multilined.json'에는 몇 가지 들여쓰기된 JSON 레코드가 있습니다. 형식은 `multijson` 엔진이 JSON 구조로 레코드를 읽도록 지시합니다.

# <a name="kql"></a>[KQL](#tab/kusto-query-language)

테이블에 데이터를 수집합니다. `Events`

```kusto
.ingest into table Events h'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/multilined.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D' with (format=multijson, jsonMappingReference=FlatEventMapping)
```

# <a name="c"></a>[C#](#tab/c-sharp)

테이블에 데이터를 수집합니다. `Events`

```csharp
var tableMapping = "FlatEventMapping";
var blobPath = "https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/multilined.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D";
var properties =
    new KustoQueuedIngestionProperties(database, table)
    {
        Format = DataSourceFormat.multijson,
        IngestionMappingReference = tableMapping
    };

ingestClient.IngestFromSingleBlob(blobPath, deleteSourceOnSuccess: false, ingestionProperties: properties);
```

# <a name="python"></a>[Python](#tab/python)

테이블에 데이터를 수집합니다. `Events`

```python
MAPPING = "FlatEventMapping"
BLOB_PATH = 'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/multilined.JSON?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D'
INGESTION_PROPERTIES = IngestionProperties(database=DATABASE, table=TABLE, dataFormat=DataFormat.multijson, mappingReference=MAPPING)
BLOB_DESCRIPTOR = BlobDescriptor(BLOB_PATH, FILE_SIZE)
INGESTION_CLIENT.ingest_from_blob(
    BLOB_DESCRIPTOR, ingestion_properties=INGESTION_PROPERTIES)
```

---

## <a name="ingest-json-records-containing-arrays"></a>배열을 포함하는 JSON 레코드 를 인제스트

배열 데이터 형식은 정렬된 값의 컬렉션입니다. JSON 배열의 인기는 업데이트 [정책에](kusto/management/update-policy.md)의해 수행됩니다. JSON은 중간 테이블에 있는 것처럼 섭취됩니다. 업데이트 정책은 `RawEvents` 테이블에서 미리 정의된 함수를 실행하여 결과를 대상 테이블로 다시 조정합니다. 다음과 같은 구조로 데이터를 수집합니다.

```json
{
    "records": 
    [
        {
            "timestamp": "2019-05-02 15:23:50.0000000",
            "deviceId": "ddbc1bf5-096f-42c0-a771-bc3dca77ac71",
            "messageId": "7f316225-839a-4593-92b5-1812949279b3",
            "temperature": 31.0301639051317,
            "humidity": 62.0791099602725
        },
        {
            "timestamp": "2019-05-02 15:23:51.0000000",
            "deviceId": "ddbc1bf5-096f-42c0-a771-bc3dca77ac71",
            "messageId": "57de2821-7581-40e4-861e-ea3bde102364",
            "temperature": 33.7529423105311,
            "humidity": 75.4787976739364
        }
    ]
}
```

# <a name="kql"></a>[KQL](#tab/kusto-query-language)

1. 컬렉션의 `update policy` `records` 각 값이 연산자를 사용하여 별도의 행을 수신할 수 `mv-expand` 있도록 컬렉션을 확장하는 함수를 만듭니다. 테이블을 `RawEvents` 원본 테이블과 `Events` 대상 테이블로 사용합니다.

    ```kusto
    .create function EventRecordsExpand() {
        RawEvents
        | mv-expand records = Event
        | project
            Time = todatetime(records["timestamp"]),
            Device = tostring(records["deviceId"]),
            MessageId = tostring(records["messageId"]),
            Temperature = todouble(records["temperature"]),
            Humidity = todouble(records["humidity"])
    }
    ```

1. 함수에서 받은 스키마는 대상 테이블의 스키마와 일치해야 합니다. 연산자를 사용하여 `getschema` 스키마를 검토합니다.

    ```kusto
    EventRecordsExpand() | getschema
    ```

1. 대상 테이블에 업데이트 정책을 추가합니다. 이 정책은 중간 테이블에서 새로 수집된 데이터에 대해 `RawEvents` 쿼리를 자동으로 실행하고 결과를 `Events` 테이블에 수집합니다. 중간 테이블이 유지되지 않도록 0 보존 정책을 정의합니다.

    ```kusto
    .alter table Events policy update @'[{"Source": "RawEvents", "Query": "EventRecordsExpand()", "IsEnabled": "True"}]'
    ```

1. 테이블에 데이터를 수집합니다. `RawEvents`

    ```kusto
    .ingest into table RawEvents h'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/array.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D' with (format=multijson, jsonMappingReference=RawEventMapping)
    ```

1. 표의 데이터를 `Events` 검토합니다.

    ```kusto
    Events
    ```

# <a name="c"></a>[C#](#tab/c-sharp)

1. 컬렉션의 `records` 각 값이 연산자를 사용하여 별도의 행을 수신할 수 `mv-expand` 있도록 컬렉션을 확장하는 update 함수를 만듭니다. 테이블을 `RawEvents` 원본 테이블과 `Events` 대상 테이블로 사용합니다.   

    ```csharp
    var command =
        CslCommandGenerator.GenerateCreateFunctionCommand(
            "EventRecordsExpand",
            "UpdateFunctions",
            string.Empty,
            null,
            @"RawEvents
                | mv-expand records = Event
                | project
                    Time = todatetime(records['timestamp']),
                    Device = tostring(records['deviceId']),
                    MessageId = tostring(records['messageId']),
                    Temperature = todouble(records['temperature']),
                    Humidity = todouble(records['humidity'])",
            ifNotExists: false);

    kustoClient.ExecuteControlCommand(command);
    ```

    > [!NOTE]
    > 함수에서 받은 스키마는 대상 테이블의 스키마와 일치해야 합니다.

1. 대상 테이블에 업데이트 정책을 추가합니다. 이 정책은 중간 테이블에서 새로 수집된 데이터에 대해 `RawEvents` 쿼리를 자동으로 실행하고 그 `Events` 결과를 테이블에 수집합니다. 중간 테이블이 유지되지 않도록 0 보존 정책을 정의합니다.

    ```csharp
    var command =
        ".alter table Events policy update @'[{'Source': 'RawEvents', 'Query': 'EventRecordsExpand()', 'IsEnabled': 'True'}]";

    kustoClient.ExecuteControlCommand(command);
    ```

1. 테이블에 데이터를 수집합니다. `RawEvents`

    ```csharp
    var table = "RawEvents";
    var tableMapping = "RawEventMapping";
    var blobPath = "https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/array.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D";
    var properties =
        new KustoQueuedIngestionProperties(database, table)
        {
            Format = DataSourceFormat.multijson,
            IngestionMappingReference = tableMapping
        };

    ingestClient.IngestFromSingleBlob(blobPath, deleteSourceOnSuccess: false, ingestionProperties: properties);
    ```
    
1. 표의 데이터를 `Events` 검토합니다.

# <a name="python"></a>[Python](#tab/python)

1. 컬렉션의 `records` 각 값이 연산자를 사용하여 별도의 행을 수신할 수 `mv-expand` 있도록 컬렉션을 확장하는 update 함수를 만듭니다. 테이블을 `RawEvents` 원본 테이블과 `Events` 대상 테이블로 사용합니다.   

    ```python
    CREATE_FUNCTION_COMMAND = 
        '''.create function EventRecordsExpand() { 
            RawEvents
            | mv-expand records = Event
            | project
                Time = todatetime(records["timestamp"]),
                Device = tostring(records["deviceId"]),
                MessageId = tostring(records["messageId"]),
                Temperature = todouble(records["temperature"]),
                Humidity = todouble(records["humidity"])
            }'''
    RESPONSE = KUSTO_CLIENT.execute_mgmt(DATABASE, CREATE_FUNCTION_COMMAND)
    dataframe_from_result_table(RESPONSE.primary_results[0])
    ```

    > [!NOTE]
    > 함수에서 수신한 스키마는 대상 테이블의 스키마와 일치해야 합니다.

1. 대상 테이블에 업데이트 정책을 추가합니다. 이 정책은 중간 테이블에서 새로 수집된 데이터에 대해 `RawEvents` 쿼리를 자동으로 실행하고 그 `Events` 결과를 테이블에 수집합니다. 중간 테이블이 유지되지 않도록 0 보존 정책을 정의합니다.

    ```python
    CREATE_UPDATE_POLICY_COMMAND = 
        """.alter table Events policy update @'[{'Source': 'RawEvents', 'Query': 'EventRecordsExpand()', 'IsEnabled': 'True'}]"""
    RESPONSE = KUSTO_CLIENT.execute_mgmt(DATABASE, CREATE_UPDATE_POLICY_COMMAND)
    dataframe_from_result_table(RESPONSE.primary_results[0])
    ```

1. 테이블에 데이터를 수집합니다. `RawEvents`

    ```python
    TABLE = "RawEvents"
    MAPPING = "RawEventMapping"
    BLOB_PATH = 'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/array.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D'
    INGESTION_PROPERTIES = IngestionProperties(database=DATABASE, table=TABLE, dataFormat=DataFormat.multijson, mappingReference=MAPPING)
    BLOB_DESCRIPTOR = BlobDescriptor(BLOB_PATH, FILE_SIZE)
    INGESTION_CLIENT.ingest_from_blob(
        BLOB_DESCRIPTOR, ingestion_properties=INGESTION_PROPERTIES)
    ```

1. 표의 데이터를 `Events` 검토합니다.

---    

## <a name="ingest-json-records-containing-dictionaries"></a>사전을 포함하는 JSON 레코드 를 인제스트

사전 구조화 JSON키 값 쌍을 포함합니다. Json 레코드는 에서 논리적 식을 `JsonPath`사용하여 인식 매핑을 거칩니다. 다음 구조로 데이터를 수집할 수 있습니다.

```json
{
    "event": 
    [
        {
            "Key": "timestamp",
            "Value": "2019-05-02 15:23:50.0000000"
        },
        {
            "Key": "deviceId",
            "Value": "ddbc1bf5-096f-42c0-a771-bc3dca77ac71"
        },
        {
            "Key": "messageId",
            "Value": "7f316225-839a-4593-92b5-1812949279b3"
        },
        {
            "Key": "temperature",
            "Value": 31.0301639051317
        },
        {
            "Key": "humidity",
            "Value": 62.0791099602725
        }
    ]
}
```

# <a name="kql"></a>[KQL](#tab/kusto-query-language)

1. JSON 매핑을 만듭니다.

    ```kusto
    .create table Events ingestion json mapping 'KeyValueEventMapping' '[{"column":"Time","path":"$.event[?(@.Key == 'timestamp')]"},{"column":"Device","path":"$.event[?(@.Key == 'deviceId')]"},{"column":"MessageId","path":"$.event[?(@.Key == 'messageId')]"},{"column":"Temperature","path":"$.event[?(@.Key == 'temperature')]"},{"column":"Humidity","path":"$.event[?(@.Key == 'humidity')]"}]'
    ```

1. 테이블에 데이터를 수집합니다. `Events`

    ```kusto
    .ingest into table Events h'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/dictionary.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D' with (format=multijson, jsonMappingReference=KeyValueEventMapping)
    ```

# <a name="c"></a>[C#](#tab/c-sharp)

1. JSON 매핑을 만듭니다.

    ```csharp
    var tableName = "Events";
    var tableMapping = "KeyValueEventMapping";
    var command =
        CslCommandGenerator.GenerateTableJsonMappingCreateCommand(
            tableName,
            tableMapping,
            new[]
            {
                        new JsonColumnMapping {ColumnName = "Time", JsonPath = "$.event[?(@.Key == 'timestamp')]"},
                        new JsonColumnMapping {ColumnName = "Device", JsonPath = "$.event[?(@.Key == 'deviceId')]"},
                        new JsonColumnMapping {ColumnName = "MessageId", JsonPath = "$.event[?(@.Key == 'messageId')]"},
                        new JsonColumnMapping {ColumnName = "Temperature", JsonPath = "$.event[?(@.Key == 'temperature')]"},
                        new JsonColumnMapping {ColumnName = "Humidity", JsonPath = "$.event[?(@.Key == 'humidity')]"},
            });

    kustoClient.ExecuteControlCommand(command);
    ```

1. 테이블에 데이터를 수집합니다. `Events`

    ```csharp
    var blobPath = "https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/dictionary.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D";
    var properties =
        new KustoQueuedIngestionProperties(database, table)
        {
            Format = DataSourceFormat.multijson,
            IngestionMappingReference = tableMapping
        };

    ingestClient.IngestFromSingleBlob(blobPath, deleteSourceOnSuccess: false, ingestionProperties: properties);
    ```

# <a name="python"></a>[Python](#tab/python)

1. JSON 매핑을 만듭니다.

    ```python
    MAPPING = "KeyValueEventMapping"
    CREATE_MAPPING_COMMAND = ".create table Events ingestion json mapping '" + MAPPING + """' '[{"column":"Time","path":"$.event[?(@.Key == 'timestamp')]"},{"column":"Device","path":"$.event[?(@.Key == 'deviceId')]"},{"column":"MessageId","path":"$.event[?(@.Key == 'messageId')]"},{"column":"Temperature","path":"$.event[?(@.Key == 'temperature')]"},{"column":"Humidity","path":"$.event[?(@.Key == 'humidity')]"}]'""" 
    RESPONSE = KUSTO_CLIENT.execute_mgmt(DATABASE, CREATE_MAPPING_COMMAND)
    dataframe_from_result_table(RESPONSE.primary_results[0])
    ```

1. 테이블에 데이터를 수집합니다. `Events`

     ```python
    MAPPING = "KeyValueEventMapping"
    BLOB_PATH = 'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/dictionary.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D'
    INGESTION_PROPERTIES = IngestionProperties(database=DATABASE, table=TABLE, dataFormat=DataFormat.multijson, mappingReference=MAPPING)u
    BLOB_DESCRIPTOR = BlobDescriptor(BLOB_PATH, FILE_SIZE)
    INGESTION_CLIENT.ingest_from_blob(
        BLOB_DESCRIPTOR, ingestion_properties=INGESTION_PROPERTIES)
    ```

---    

## <a name="next-steps"></a>다음 단계

* [데이터 수집 개요](ingest-data-overview.md)
* [쿼리 쓰기](write-queries.md)
