---
title: Kusto 수집 라이브러리를 사용 하 여 데이터 수집-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 Kusto. 수집 라이브러리를 사용 하 여 데이터를 수집 하는 방법을 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/05/2020
ms.openlocfilehash: fe268d19e5f42308737b7c392c58c6c1dca071b3
ms.sourcegitcommit: 061eac135a123174c85fe1afca4d4208c044c678
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/05/2020
ms.locfileid: "82799614"
---
# <a name="data-ingestion-with-the-kustoingest-library"></a>Kusto. 수집 라이브러리를 사용 하 여 데이터 수집

이 문서에서는 데이터 수집을 위해 Kusto. 수집 클라이언트 라이브러리를 사용 하는 샘플 코드를 제공 합니다. 이 코드는 지연 된 수집 이라고 하는 프로덕션 등급 파이프라인에 대 한 수집의 권장 된 모드를 자세히 설명 합니다. Kusto. 수집 라이브러리의 경우 해당 엔터티는 [IKustoQueuedIngestClient](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient) 인터페이스입니다. 클라이언트 코드는 azure 큐에 수집 알림을 게시 하 여 Azure 데이터 탐색기 서비스와 상호 작용 합니다. 수집을 담당 하는 데이터 관리 엔터티에서 큐에 대 한 참조를 가져옵니다. 

> [!NOTE]
> 데이터 관리 서비스와의 상호 작용은 Azure Active Directory (Azure AD)를 사용 하 여 인증 되어야 합니다.

이 샘플은 Azure AD 사용자 인증을 사용 하 고 대화형 사용자의 id로 실행 됩니다.

## <a name="dependencies"></a>종속성

이 샘플 코드에는 다음 NuGet 패키지가 필요 합니다.
* Microsoft. Kusto. 수집
* Microsoft.IdentityModel.Clients.ActiveDirectory
* WindowsAzure.Storage
* Newtonsoft.Json

## <a name="namespaces-used"></a>사용 되는 네임 스페이스

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Threading;
using Kusto.Data;
using Kusto.Data.Common;
using Kusto.Data.Net.Client;
using Kusto.Ingest;
```

## <a name="code"></a>코드

코드는 다음을 수행 합니다.
1. `KustoLab` 공유 Azure 데이터 탐색기 클러스터에서 `KustoIngestClientDemo` 데이터베이스에 테이블을 만듭니다.
2. 해당 테이블에서 [JSON 열 매핑 개체](../../management/create-ingestion-mapping-command.md) 를 프로 비전 합니다.
3. 데이터 관리 서비스에 대 한 IKustoQueuedIngestClient 인스턴스를 만듭니다. [IKustoQueuedIngestClient](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient) `Ingest-KustoLab`
4. 적절 한 수집 옵션으로 [KustoQueuedIngestionProperties](kusto-ingest-client-reference.md#class-kustoqueuedingestionproperties) 를 설정 합니다.
5. 생성 된 일부 데이터를 사용 하 여 수집 수 있는 MemoryStream를 만듭니다.
6. 메서드를 `KustoQueuedIngestClient.IngestFromStream` 사용 하 여 데이터 수집
7. 수집 [오류](kusto-ingest-client-status.md#tracking-ingestion-status-kustoqueuedingestclient) 를 폴링합니다.

```csharp
static void Main(string[] args)
{
    var clusterName = "KustoLab";
    var db = "KustoIngestClientDemo";
    var table = "Table1";
    var mappingName = "Table1_mapping_1";
    var serviceNameAndRegion = "clusterNameAndRegion"; // For example, "mycluster.westus"
    var authority = "AAD Tenant or name"; // For example, "microsoft.com"

    // Set up table
    var kcsbEngine =
        new KustoConnectionStringBuilder($"https://{serviceNameAndRegion}.kusto.windows.net").WithAadUserPromptAuthentication(authority: $"{authority}");

    using (var kustoAdminClient = KustoClientFactory.CreateCslAdminProvider(kcsbEngine))
    {
        var columns = new List<Tuple<string, string>>()
        {
            new Tuple<string, string>("Column1", "System.Int64"),
            new Tuple<string, string>("Column2", "System.DateTime"),
            new Tuple<string, string>("Column3", "System.String"),
        };

        var command = CslCommandGenerator.GenerateTableCreateCommand(table, columns);
        kustoAdminClient.ExecuteControlCommand(databaseName: db, command: command);

        // Set up mapping
        var columnMappings = new List<JsonColumnMapping>();
        columnMappings.Add(new JsonColumnMapping()
            { ColumnName = "Column1", JsonPath = "$.Id" });
        columnMappings.Add(new JsonColumnMapping()
            { ColumnName = "Column2", JsonPath = "$.Timestamp" });
        columnMappings.Add(new JsonColumnMapping()
            { ColumnName = "Column3", JsonPath = "$.Message" });

        command = CslCommandGenerator.GenerateTableJsonMappingCreateCommand(
                                            table, mappingName, columnMappings);
        kustoAdminClient.ExecuteControlCommand(databaseName: db, command: command);
    }

    // Create Ingest Client
    var kcsbDM =
        new KustoConnectionStringBuilder($"https://ingest-{serviceNameAndRegion}.kusto.windows.net").WithAadUserPromptAuthentication(authority: $"{authority}");

    using (var ingestClient = KustoIngestFactory.CreateQueuedIngestClient(kcsbDM))
    {
        var ingestProps = new KustoQueuedIngestionProperties(db, table);
        // For the sake of getting both failure and success notifications we set this to IngestionReportLevel.FailuresAndSuccesses
        // Usually the recommended level is IngestionReportLevel.FailuresOnly
        ingestProps.ReportLevel = IngestionReportLevel.FailuresAndSuccesses;
        ingestProps.ReportMethod = IngestionReportMethod.Queue;
        ingestProps.JSONMappingReference = mappingName;
        ingestProps.Format = DataSourceFormat.json;

        // Prepare data for ingestion
        using (var memStream = new MemoryStream())
        using (var writer = new StreamWriter(memStream))
        {
            for (int counter = 1; counter <= 10; ++counter)
            {
                writer.WriteLine(
                    "{{ \"Id\":\"{0}\", \"Timestamp\":\"{1}\", \"Message\":\"{2}\" }}",
                    counter, DateTime.UtcNow.AddSeconds(100 * counter),
                    $"This is a dummy message number {counter}");
            }

            writer.Flush();
            memStream.Seek(0, SeekOrigin.Begin);

            // Post ingestion message
            ingestClient.IngestFromStream(memStream, ingestProps, leaveOpen: true);
        }

        // Wait and retrieve all notifications
        //  - Actual duration should be decided based on the effective Ingestion Batching Policy set on the table/database
        Thread.Sleep(<timespan>);
        var errors = ingestClient.GetAndDiscardTopIngestionFailures().GetAwaiter().GetResult();
        var successes = ingestClient.GetAndDiscardTopIngestionSuccesses().GetAwaiter().GetResult();

        errors.ForEach((f) => { Console.WriteLine($"Ingestion error: {f.Info.Details}"); });
        successes.ForEach((s) => { Console.WriteLine($"Ingested: {s.Info.IngestionSourcePath}"); });
    }
}
```
