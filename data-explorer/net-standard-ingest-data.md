---
title: Azure 데이터 탐색기 .NET Standard SDK (미리 보기)를 사용 하 여 데이터 수집
description: 이 문서에서는 .NET Standard SDK를 사용 하 여 데이터를 Azure 데이터 탐색기 수집 (로드) 하는 방법에 대해 알아봅니다.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 05/19/2020
ms.openlocfilehash: e24ebc2b89c7890f8818b0491c0cd6b8fbfd85fe
ms.sourcegitcommit: ee90472a4f9d751d4049744d30e5082029c1b8fa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/21/2020
ms.locfileid: "83722153"
---
# <a name="ingest-data-using-the-azure-data-explorer-net-standard-sdk-preview"></a>Azure Data Explorer .NET Standard SDK(미리 보기)를 사용하여 데이터 수집

ADX(Azure Data Explorer)는 로그 및 원격 분석 데이터에 사용 가능한 빠르고 확장성이 우수한 데이터 탐색 서비스입니다. ADX는 2개의 .NET Standard용 클라이언트 라이브러리, [수집 라이브러리](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Ingest.NETStandard) 및 [데이터 라이브러리](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Data.NETStandard)를 제공합니다. 이러한 라이브러리를 사용하여 데이터를 클러스터로 수집(로드)하고 코드에서 데이터를 쿼리할 수 있습니다. 이 문서에서는 먼저 테스트 클러스터에서 테이블 및 데이터 매핑을 만듭니다. 그런 다음, 클러스터 큐에 수집을 넣고 결과의 유효성을 검사합니다.

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독이 아직 없는 경우 시작하기 전에 [Azure 체험 계정](https://azure.microsoft.com/free/)을 만듭니다.

* [테스트 클러스터 및 데이터베이스](create-cluster-database-portal.md)

## <a name="install-the-ingest-library"></a>수집 라이브러리 설치

```
Install-Package Microsoft.Azure.Kusto.Ingest.NETStandard
```

## <a name="authentication"></a>인증

애플리케이션을 인증하기 위해 Azure 데이터 탐색기는 AAD 테넌트 ID를 사용합니다. 테넌트 ID를 찾으려면 다음 URL을 사용하여 *YourDomain*을 사용자 도메인으로 대체합니다.

```
https://login.windows.net/<YourDomain>/.well-known/openid-configuration/
```

예를 들어 도메인이 *contoso.com*인 경우 URL은 [https://login.windows.net/contoso.com/.well-known/openid-configuration/](https://login.windows.net/contoso.com/.well-known/openid-configuration/)입니다. 결과를 보려면 이 URL을 클릭합니다. 첫 번째 줄은 다음과 같습니다. 

```
"authorization_endpoint":"https://login.windows.net/6babcaad-604b-40ac-a9d7-9fd97c0b779f/oauth2/authorize"
```

이 경우의 테넌트 ID는 `6babcaad-604b-40ac-a9d7-9fd97c0b779f`입니다.

이 예제에서는 AAD 사용자 및 암호를 인증에 사용하여 클러스터에 액세스합니다. 또한 AAD 애플리케이션 인증서 및 AAD 애플리케이션 키를 사용할 수도 있습니다. 이 코드를 실행하기 전에 `tenantId`, `user` 및 `password`의 값을 설정합니다.

```csharp
var tenantId = "<TenantId>";
var user = "<User>";
var password = "<Password>";
```

## <a name="construct-the-connection-string"></a>연결 문자열 구성
이제 연결 문자열을 구성합니다. 이후 단계에서 대상 테이블 및 매핑을 만듭니다.

```csharp
var kustoUri = "https://<ClusterName>.<Region>.kusto.windows.net:443/";
var database = "<DatabaseName>";

var kustoConnectionStringBuilder =
    new KustoConnectionStringBuilder(kustoUri)
    {
        FederatedSecurity = true,
        InitialCatalog = database,
        UserID = user,
        Password = password,
        Authority = tenantId
    };
```

## <a name="set-source-file-information"></a>소스 파일 정보 설정

원본 파일의 경로를 설정합니다. 이 예제에서는 Azure Blob Storage에 호스트된 예제 파일을 사용합니다. **Stormevents** 샘플 데이터 집합에는 [환경적 정보에 대 한 국가별 센터](https://www.ncdc.noaa.gov/stormevents/)의 날씨 관련 데이터가 포함 되어 있습니다.

```csharp
var blobPath = "https://kustosamplefiles.blob.core.windows.net/samplefiles/StormEvents.csv?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D";
```

## <a name="create-a-table-on-your-test-cluster"></a>테스트 클러스터에 테이블 만들기
`StormEvents.csv` 파일에 있는 데이터 스키마와 일치하는 `StormEvents`라는 테이블을 만듭니다.

```csharp
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

    kustoClient.ExecuteControlCommand(command);
}
```

## <a name="define-ingestion-mapping"></a>수집 매핑 정의

테이블을 만들 때 사용되는 열 이름에 들어오는 CSV 데이터를 매핑합니다.
해당 테이블에서 [CSV 열 매핑 개체](kusto/management/create-ingestion-mapping-command.md) 프로비전

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

    kustoClient.ExecuteControlCommand(command);
}
```

## <a name="queue-a-message-for-ingestion"></a>수집을 위해 메시지를 큐에 넣음

Blob Storage에서 데이터를 끌어온 후 ADX에 수집하기 위해 메시지를 큐에 넣습니다.

```csharp
var ingestUri = "https://ingest-<ClusterName>.<Region>.kusto.windows.net:443/";
var ingestConnectionStringBuilder =
    new KustoConnectionStringBuilder(ingestUri)
    {
        FederatedSecurity = true,
        InitialCatalog = database,
        UserID = user,
        Password = password,
        Authority = tenantId
    };

using (var ingestClient = KustoIngestFactory.CreateQueuedIngestClient(ingestConnectionStringBuilder))
{
    var properties =
        new KustoQueuedIngestionProperties(database, table)
        {
            Format = DataSourceFormat.csv,
            IngestionMapping = new IngestionMapping()
            { 
                IngestionMappingReference = tableMapping
            },
            IgnoreFirstRecord = true
        };

    ingestClient.IngestFromStorageAsync(blobPath ingestionProperties: properties);
}
```

## <a name="validate-data-was-ingested-into-the-table"></a>데이터가 테이블에 수집되었는지 검증

큐에 넣은 수집이 예약되고 데이터가 ADX에 로드될 때까지 5~10분 정도 기다립니다. 그런 후, 다음 코드를 실행하여 `StormEvents` 테이블의 레코드 수를 가져옵니다.

```csharp
using (var cslQueryProvider = KustoClientFactory.CreateCslQueryProvider(kustoConnectionStringBuilder))
{
    var query = $"{table} | count";

    var results = cslQueryProvider.ExecuteQuery<long>(query);
    Console.WriteLine(results.Single());
}
```

## <a name="run-troubleshooting-queries"></a>쿼리 문제 해결 실행

에 로그인 하 [https://dataexplorer.azure.com](https://dataexplorer.azure.com) 고 클러스터에 연결 합니다. 데이터베이스에서 다음 명령을 실행하여 지난 4시간 동안 수집 실패가 있었는지 확인합니다. 실행하기 전에 데이터베이스 이름을 바꿉니다.

```Kusto
.show ingestion failures
| where FailedOn > ago(4h) and Database == "<DatabaseName>"
```

다음 명령을 실행하여 지난 4시간 동안 진행된 모든 수집 작업의 상태를 확인합니다. 실행하기 전에 데이터베이스 이름을 바꿉니다.

```Kusto
.show operations
| where StartedOn > ago(4h) and Database == "<DatabaseName>" and Operation == "DataIngestPull"
| summarize arg_max(LastUpdatedOn, *) by OperationId
```

## <a name="clean-up-resources"></a>리소스 정리

다른 문서를 따르려면 만든 리소스를 유지 합니다. 그렇지 않으면 데이터베이스에서 다음 명령을 실행하여 `StormEvents` 테이블을 정리합니다.

```Kusto
.drop table StormEvents
```

## <a name="next-steps"></a>다음 단계

* [쿼리 쓰기](write-queries.md)
