---
title: Kusto.Ingest 라이브러리를 사용하여 HowTo 데이터 수집 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기에서 Kusto.Ingest 라이브러리를 사용하여 HowTo 데이터 수집에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/05/2020
ms.openlocfilehash: 80b2b61c70269c5bd166a064fe9d0e2c59dd8197
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81523633"
---
# <a name="howto-data-ingestion-with-kustoingest-library"></a>Kusto.Ingest 라이브러리를 사용하여 데이터 수집 방법
이 문서에서는 Kusto.Ingest 클라이언트 라이브러리를 사용하는 샘플 코드를 제공합니다.

## <a name="overview"></a>개요
다음 코드 샘플은 Kusto.Ingest 라이브러리를 사용하여 Kusto로의 큐어(Kusto 데이터 관리 서비스를 통해) 데이터 수집을 보여 줍니다.

> 이 문서에서는 **큐잉** 된 생성이라고도 하는 프로덕션 급 파이프라인에 대한 권장 작업 모드를 다룹니다(Kusto.Ingest 라이브러리의 관점에서 해당 엔터티는 [IKustoQueuedIngestClient](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient) 인터페이스). 이 모드에서 클라이언트 코드는 Kusto 데이터 관리(일명)에서 가져온 참조인 Azure 큐에 수집 알림 메시지를 게시하여 Kusto 서비스와 상호 작용합니다. 섭취) 서비스. 데이터 관리 서비스와의 상호 작용은 **AAD.**

#### <a name="authentication"></a>인증
이 코드 샘플은 AAD 사용자 인증을 사용하고 대화형 사용자의 ID로 실행됩니다.

## <a name="dependencies"></a>종속성
이 샘플 코드에는 다음 NuGet 패키지가 필요합니다.
* 마이크로소프트.쿠스토.잉게스트
* Microsoft.IdentityModel.Clients.ActiveDirectory
* WindowsAzure.Storage
* Newtonsoft.Json

## <a name="namespaces-used"></a>사용된 네임스페이스
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
아래 표시되는 코드는 다음을 수행합니다.
1. 데이터베이스 에서 `KustoIngestClientDemo` `KustoLab` 공유 Kusto 클러스터에 테이블 을 만듭니다.
2. 해당 테이블에 [JSON 열 매핑 개체](../../management/create-ingestion-mapping-command.md) 프로비전
3. 데이터 관리 서비스에 대한 [IKustoQueuedIngingestClient](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient) 인스턴스 생성 `Ingest-KustoLab`
4. [KustoQueuedingestion적절한](kusto-ingest-client-reference.md#class-kustoqueuedingestionproperties) 섭취 옵션을 사용하여 속성 설정
5. 수집할 생성된 데이터로 채워진 메모리스트림을 만듭니다.
6. 방법을 사용하여 `KustoQueuedIngestClient.IngestFromStream` 데이터 수집
7. [모든 인기 오류에](kusto-ingest-client-status.md#tracking-ingestion-status-kustoqueuedingestclient) 대한 설문 조사

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