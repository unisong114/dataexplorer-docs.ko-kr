---
title: Azure 데이터 탐색기 .NET SDK를 사용 하 여 데이터 수집
description: 이 문서에서는 .NET SDK를 사용 하 여 Azure 데이터 탐색기에 데이터를 수집 (로드) 하는 방법에 대해 알아봅니다.
author: orspod
ms.author: orspodek
ms.reviewer: vladikb
ms.service: data-explorer
ms.topic: how-to
ms.date: 07/07/2020
ms.openlocfilehash: 56fd2d253cac80f097caa9206cd3b7c2a9d3c118
ms.sourcegitcommit: 898f67b83ae8cf55e93ce172a6fd3473b7c1c094
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/21/2020
ms.locfileid: "92343507"
---
# <a name="ingest-data-using-the-azure-data-explorer-net-sdk"></a>Azure 데이터 탐색기 .NET SDK를 사용 하 여 데이터 수집 

> [!div class="op_single_selector"]
> * [.NET](net-sdk-ingest-data.md)
> * [Python](python-ingest-data.md)
> * [Node](node-ingest-data.md)
> * [Go](go-ingest-data.md)

Azure 데이터 탐색기는 로그 및 원격 분석 데이터에 사용 가능한 빠르고 확장성이 우수한 데이터 탐색 서비스입니다. .NET 용 클라이언트 라이브러리 두 개 ( [수집 라이브러리](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Ingest/) 및 [데이터 라이브러리](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Data/))를 제공 합니다. .NET SDK에 대 한 자세한 내용은 [.NET sdk 정보](./kusto/api/netfx/about-the-sdk.md)를 참조 하세요.
이러한 라이브러리를 사용하여 데이터를 클러스터로 수집(로드)하고 코드에서 데이터를 쿼리할 수 있습니다. 이 문서에서는 먼저 테스트 클러스터에서 테이블 및 데이터 매핑을 만듭니다. 그런 다음, 클러스터 큐에 수집을 넣고 결과의 유효성을 검사합니다.

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독이 아직 없는 경우 시작하기 전에 [Azure 체험 계정](https://azure.microsoft.com/free/)을 만듭니다.

* [테스트 클러스터 및 데이터베이스](create-cluster-database-portal.md)

## <a name="install-the-ingest-library"></a>수집 라이브러리 설치

```azurecli
Install-Package Microsoft.Azure.Kusto.Ingest
```

## <a name="add-authentication-and-construct-connection-string"></a>인증 추가 및 연결 문자열 생성

### <a name="authentication"></a>인증

응용 프로그램을 인증 하기 위해 Azure 데이터 탐색기 SDK는 AAD 테 넌 트 ID를 사용 합니다. 테넌트 ID를 찾으려면 다음 URL을 사용하여 *YourDomain*을 사용자 도메인으로 대체합니다.

```http
https://login.windows.net/<YourDomain>/.well-known/openid-configuration/
```

예를 들어 도메인이 *contoso.com*인 경우 URL은 [https://login.windows.net/contoso.com/.well-known/openid-configuration/](https://login.windows.net/contoso.com/.well-known/openid-configuration/)입니다. 결과를 보려면 이 URL을 클릭합니다. 첫 번째 줄은 다음과 같습니다. 

```console
"authorization_endpoint":"https://login.windows.net/6babcaad-604b-40ac-a9d7-9fd97c0b779f/oauth2/authorize"
```

이 경우의 테넌트 ID는 `6babcaad-604b-40ac-a9d7-9fd97c0b779f`입니다.

이 예제에서는 대화형 AAD 사용자 인증을 사용 하 여 클러스터에 액세스 합니다. AAD 응용 프로그램 인증을 인증서 또는 응용 프로그램 암호와 함께 사용할 수도 있습니다. `tenantId`이 코드를 실행 하기 전에 및에 대 한 올바른 값을 설정 해야 `clusterUri` 합니다. 

Azure 데이터 탐색기 SDK는 연결 문자열의 일부로 인증 방법을 설정 하는 편리한 방법을 제공 합니다. Azure 데이터 탐색기 연결 문자열에 대 한 전체 설명서는 [연결 문자열](kusto/api/connection-strings/kusto.md)을 참조 하세요.

> [!NOTE]
> 최신 버전의 SDK는 .NET Core에서 대화형 uer 인증을 지원 하지 않습니다. 필요한 경우 AAD 사용자 이름/암호 또는 응용 프로그램 인증을 대신 사용 합니다.

### <a name="construct-the-connection-string"></a>연결 문자열 구성

이제 Azure 데이터 탐색기 연결 문자열을 구성할 수 있습니다. 이후 단계에서 대상 테이블 및 매핑을 만듭니다.

```csharp
var tenantId = "<TenantId>";
var kustoUri = "https://<ClusterName>.<Region>.kusto.windows.net/";

var kustoConnectionStringBuilder = new KustoConnectionStringBuilder(kustoUri).WithAadUserPromptAuthentication(tenantId);
```

## <a name="set-source-file-information"></a>소스 파일 정보 설정

원본 파일의 경로를 설정합니다. 이 예제에서는 Azure Blob Storage에 호스트된 예제 파일을 사용합니다. **Stormevents** 샘플 데이터 집합에는 [환경적 정보에 대 한 국가별 센터](https://www.ncdc.noaa.gov/stormevents/)의 날씨 관련 데이터가 포함 되어 있습니다.

```csharp
var blobPath = "https://kustosamplefiles.blob.core.windows.net/samplefiles/StormEvents.csv?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D";
```

## <a name="create-a-table-on-your-test-cluster"></a>테스트 클러스터에 테이블 만들기

`StormEvents.csv` 파일에 있는 데이터 스키마와 일치하는 `StormEvents`라는 테이블을 만듭니다.

> [!TIP]
> 다음 코드 조각에서는 거의 모든 호출에 대해 클라이언트의 인스턴스를 만듭니다. 이 작업은 각 조각을 개별적으로 실행할 수 있도록 하기 위한 것입니다. 프로덕션에서 클라이언트 인스턴스는 재진입 이며 필요에 따라 유지 해야 합니다. 여러 데이터베이스로 작업 하는 경우에도 URI 당 단일 클라이언트 인스턴스도 충분 합니다. 데이터베이스는 명령 수준에서 지정 될 수 있습니다.

```csharp
var databaseName = "<DatabaseName>";
var table = "StormEvents";
using (var kustoClient = KustoClientFactory.CreateCslAdminProvider(kustoConnectionStringBuilder))
{
    var command =
        CslCommandGenerator.GenerateTableCreateCommand(
            table,
            new[]
            {
                Tuple.Create("StartTime", "System.DateTime"),
                Tuple.Create("EndTime", "System.DateTime"),
                Tuple.Create("EpisodeId", "System.Int32"),
                Tuple.Create("EventId", "System.Int32"),
                Tuple.Create("State", "System.String"),
                Tuple.Create("EventType", "System.String"),
                Tuple.Create("InjuriesDirect", "System.Int32"),
                Tuple.Create("InjuriesIndirect", "System.Int32"),
                Tuple.Create("DeathsDirect", "System.Int32"),
                Tuple.Create("DeathsIndirect", "System.Int32"),
                Tuple.Create("DamageProperty", "System.Int32"),
                Tuple.Create("DamageCrops", "System.Int32"),
                Tuple.Create("Source", "System.String"),
                Tuple.Create("BeginLocation", "System.String"),
                Tuple.Create("EndLocation", "System.String"),
                Tuple.Create("BeginLat", "System.Double"),
                Tuple.Create("BeginLon", "System.Double"),
                Tuple.Create("EndLat", "System.Double"),
                Tuple.Create("EndLon", "System.Double"),
                Tuple.Create("EpisodeNarrative", "System.String"),
                Tuple.Create("EventNarrative", "System.String"),
                Tuple.Create("StormSummary", "System.Object"),
            });

    kustoClient.ExecuteControlCommand(databaseName, command);
}
```

## <a name="define-ingestion-mapping"></a>수집 매핑 정의

테이블을 만들 때 사용되는 열 이름에 들어오는 CSV 데이터를 매핑합니다.
해당 테이블에 [CSV 열 매핑 개체](kusto/management/create-ingestion-mapping-command.md) 를 프로 비전 합니다.

```csharp
var tableMapping = "StormEvents_CSV_Mapping";
using (var kustoClient = KustoClientFactory.CreateCslAdminProvider(kustoConnectionStringBuilder))
{
    var command =
        CslCommandGenerator.GenerateTableMappingCreateCommand(
            Data.Ingestion.IngestionMappingKind.Csv,
            table,
            tableMapping,
            new[] {
                new ColumnMapping() { ColumnName = "StartTime", Properties = new Dictionary<string, string>() { { MappingConsts.Ordinal, "0" } } },
                new ColumnMapping() { ColumnName = "EndTime", Properties =  new Dictionary<string, string>() { { MappingConsts.Ordinal, "1" } } },
                new ColumnMapping() { ColumnName = "EpisodeId", Properties = new Dictionary<string, string>() { { MappingConsts.Ordinal, "2" } } },
                new ColumnMapping() { ColumnName = "EventId", Properties =  new Dictionary<string, string>() { { MappingConsts.Ordinal, "3" } } },
                new ColumnMapping() { ColumnName = "State", Properties =  new Dictionary<string, string>() { { MappingConsts.Ordinal, "4" } } },
                new ColumnMapping() { ColumnName = "EventType", Properties =  new Dictionary<string, string>() { { MappingConsts.Ordinal, "5" } } },
                new ColumnMapping() { ColumnName = "InjuriesDirect", Properties =  new Dictionary<string, string>() { { MappingConsts.Ordinal, "6" } } },
                new ColumnMapping() { ColumnName = "InjuriesIndirect", Properties =  new Dictionary<string, string>() { { MappingConsts.Ordinal, "7" } } },
                new ColumnMapping() { ColumnName = "DeathsDirect", Properties =  new Dictionary<string, string>() { { MappingConsts.Ordinal, "8" } } },
                new ColumnMapping() { ColumnName = "DeathsIndirect", Properties =  new Dictionary<string, string>() { { MappingConsts.Ordinal, "9" } } },
                new ColumnMapping() { ColumnName = "DamageProperty", Properties =  new Dictionary<string, string>() { { MappingConsts.Ordinal, "10" } } },
                new ColumnMapping() { ColumnName = "DamageCrops", Properties =  new Dictionary<string, string>() { { MappingConsts.Ordinal, "11" } } },
                new ColumnMapping() { ColumnName = "Source", Properties =  new Dictionary<string, string>() { { MappingConsts.Ordinal, "12" } } },
                new ColumnMapping() { ColumnName = "BeginLocation", Properties =  new Dictionary<string, string>() { { MappingConsts.Ordinal, "13" } } },
                new ColumnMapping() { ColumnName = "EndLocation", Properties =  new Dictionary<string, string>() { { MappingConsts.Ordinal, "14" } } },
                new ColumnMapping() { ColumnName = "BeginLat", Properties =  new Dictionary<string, string>() { { MappingConsts.Ordinal, "15" } } },
                new ColumnMapping() { ColumnName = "BeginLon", Properties =  new Dictionary<string, string>() { { MappingConsts.Ordinal, "16" } } },
                new ColumnMapping() { ColumnName = "EndLat", Properties =  new Dictionary<string, string>() { { MappingConsts.Ordinal, "17" } } },
                new ColumnMapping() { ColumnName = "EndLon", Properties =  new Dictionary<string, string>() { { MappingConsts.Ordinal, "18" } } },
                new ColumnMapping() { ColumnName = "EpisodeNarrative", Properties =  new Dictionary<string, string>() { { MappingConsts.Ordinal, "19" } } },
                new ColumnMapping() { ColumnName = "EventNarrative", Properties =  new Dictionary<string, string>() { { MappingConsts.Ordinal, "20" } } },
                new ColumnMapping() { ColumnName = "StormSummary", Properties =  new Dictionary<string, string>() { { MappingConsts.Ordinal, "21" } } }
        });

    kustoClient.ExecuteControlCommand(databaseName, command);
}
```

## <a name="define-batching-policy-for-your-table"></a>테이블에 대 한 일괄 처리 정책 정의

Azure 데이터 탐색기 수집은 들어오는 데이터의 일괄 처리를 수행 하 여 데이터 분할 크기를 최적화 합니다. 이 프로세스는 수집 [일괄 처리 정책](kusto/management/batchingpolicy.md) 에 의해 제어 되며, 수집 [일괄 처리 정책 제어 명령](kusto/management/batching-policy.md)으로 수정할 수 있습니다. 이 정책을 사용 하 여 느린 도착 데이터의 대기 시간을 줄입니다.

```kusto
using (var kustoClient = KustoClientFactory.CreateCslAdminProvider(kustoConnectionStringBuilder))
{
    var command =
        CslCommandGenerator.GenerateTableAlterIngestionBatchingPolicyCommand(
        databaseName,
        table,
        new IngestionBatchingPolicy(maximumBatchingTimeSpan: TimeSpan.FromSeconds(10.0), maximumNumberOfItems: 100, maximumRawDataSizeMB: 1024));

    kustoClient.ExecuteControlCommand(command);
}
```

## <a name="queue-a-message-for-ingestion"></a>수집을 위해 메시지를 큐에 넣음

Blob Storage에서 데이터를 끌어온 후 Azure 데이터 탐색기에 수집하기 위해 메시지를 큐에 넣습니다. Azure 데이터 탐색기 클러스터의 데이터 수집 끝점에 대 한 연결이 설정 되 고, 다른 클라이언트가 해당 끝점을 사용 하도록 만들어집니다. 

> [!TIP]
> 다음 코드 조각에서는 거의 모든 호출에 대해 클라이언트의 인스턴스를 만듭니다. 이 작업은 각 조각을 개별적으로 실행할 수 있도록 하기 위한 것입니다. 프로덕션에서 클라이언트 인스턴스는 재진입 이며 필요에 따라 유지 해야 합니다. 여러 데이터베이스로 작업 하는 경우에도 URI 당 단일 클라이언트 인스턴스도 충분 합니다. 데이터베이스는 명령 수준에서 지정 될 수 있습니다.

```csharp
var ingestUri = "https://ingest-<ClusterName>.<Region>.kusto.windows.net";
var ingestConnectionStringBuilder = new KustoConnectionStringBuilder(ingestUri).WithAadUserPromptAuthentication(tenantId);

using (var ingestClient = KustoIngestFactory.CreateQueuedIngestClient(ingestConnectionStringBuilder))
{
    var properties =
        new KustoQueuedIngestionProperties(database, table)
        {
            Format = DataSourceFormat.csv,
            IngestionMapping = new IngestionMapping()
            { 
                IngestionMappingReference = tableMapping,
                IngestionMappingKind = IngestionMappingKind.Csv
            },
            IgnoreFirstRecord = true
        };

    ingestClient.IngestFromStorageAsync(blobPath, ingestionProperties: properties).GetAwaiter().GetResult();
}
```

## <a name="validate-data-was-ingested-into-the-table"></a>데이터가 테이블에 수집되었는지 검증

대기 중인 수집에 대해 5 ~ 10 분 동안 대기 하 여 수집을 예약 하 고 데이터를 Azure 데이터 탐색기로 로드 합니다. 그런 후, 다음 코드를 실행하여 `StormEvents` 테이블의 레코드 수를 가져옵니다.

```csharp
using (var cslQueryProvider = KustoClientFactory.CreateCslQueryProvider(kustoConnectionStringBuilder))
{
    var query = $"{table} | count";

    var results = cslQueryProvider.ExecuteQuery<long>(databaseName, query);
    Console.WriteLine(results.Single());
}
```

## <a name="run-troubleshooting-queries"></a>쿼리 문제 해결 실행

에 로그인 하 [https://dataexplorer.azure.com](https://dataexplorer.azure.com) 고 클러스터에 연결 합니다. 데이터베이스에서 다음 명령을 실행하여 지난 4시간 동안 수집 실패가 있었는지 확인합니다. 실행하기 전에 데이터베이스 이름을 바꿉니다.

```kusto
.show ingestion failures
| where FailedOn > ago(4h) and Database == "<DatabaseName>"
```

다음 명령을 실행하여 지난 4시간 동안 진행된 모든 수집 작업의 상태를 확인합니다. 실행하기 전에 데이터베이스 이름을 바꿉니다.

```kusto
.show operations
| where StartedOn > ago(4h) and Database == "<DatabaseName>" and Operation == "DataIngestPull"
| summarize arg_max(LastUpdatedOn, *) by OperationId
```

## <a name="clean-up-resources"></a>리소스 정리

다른 문서를 따르려면 만든 리소스를 유지 합니다. 그렇지 않으면 데이터베이스에서 다음 명령을 실행하여 `StormEvents` 테이블을 정리합니다.

```kusto
.drop table StormEvents
```

## <a name="next-steps"></a>다음 단계

* [쿼리 작성](write-queries.md)