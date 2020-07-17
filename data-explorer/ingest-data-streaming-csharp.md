---
title: 'C를 사용 하 여 Azure 데이터 탐색기 클러스터에서 스트리밍 수집 구성 #'
description: 'C를 사용 하 여 Azure 데이터 탐색기 클러스터를 구성 하 고 스트리밍 수집을 사용 하 여 데이터 로드를 시작 하는 방법을 알아봅니다. #'
author: orspod
ms.author: orspodek
ms.reviewer: alexefro
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/13/2020
ms.openlocfilehash: 73d1308ab38cf032eca52f772627f0946b5ea252
ms.sourcegitcommit: 537a7eaf8c8e06a5bde57503fedd1c3706dd2b45
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/16/2020
ms.locfileid: "86424265"
---
# <a name="configure-streaming-ingestion-on-your-azure-data-explorer-cluster-using-c"></a>C를 사용 하 여 Azure 데이터 탐색기 클러스터에서 스트리밍 수집 구성 #

> [!div class="op_single_selector"]
> * [포털](ingest-data-streaming.md)
> * [C#](ingest-data-streaming-csharp.md)

[!INCLUDE [ingest-data-streaming-intro](includes/ingest-data-streaming-intro.md)]

## <a name="prerequisites"></a>필수 구성 요소

* Visual Studio 2019가 설치 되어 있지 않으면 **무료** [Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/)을 다운로드 하 여 사용 합니다.  Visual Studio를 설치 하는 동안 **Azure 개발** 을 사용 하도록 설정 합니다.
* Azure 구독이 아직 없는 경우 시작하기 전에 [Azure 체험 계정](https://azure.microsoft.com/free/)을 만듭니다.
* [Azure 데이터 탐색기 클러스터 및 데이터베이스](create-cluster-database-csharp.md) 만들기
   
## <a name="enable-streaming-ingestion-on-your-cluster-using-c"></a>C를 사용 하 여 클러스터에서 스트리밍 수집 사용 #

### <a name="enable-streaming-ingestion-while-creating-a-new-cluster"></a>새 클러스터를 만드는 동안 스트리밍 수집 사용

새 Azure 데이터 탐색기 클러스터를 만드는 동안 스트리밍 수집을 사용 하도록 설정할 수 있습니다.

```csharp
...
var cluster = new Cluster(location, sku, enableStreamingIngest:true);
...
```

### <a name="enable-streaming-ingestion-on-an-existing-cluster"></a>기존 클러스터에서 스트리밍 수집 사용

Azure 데이터 탐색기 클러스터에서 스트리밍 수집을 사용 하도록 설정 하려면 다음 코드를 실행 합니다.

```csharp
    var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
    var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
    var clientSecret = "xxxxxxxxxxxxxx";//Client Secret
    var subscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";
    var authenticationContext = new AuthenticationContext($"https://login.windows.net/{tenantId}");
    var credential = new ClientCredential(clientId, clientSecret);
    var result = await authenticationContext.AcquireTokenAsync(resource: "https://management.core.windows.net/", clientCredential: credential);

    var credentials = new TokenCredentials(result.AccessToken, result.AccessTokenType);

    var kustoManagementClient = new KustoManagementClient(credentials)
    {
        SubscriptionId = subscriptionId
    };

    var resourceGroupName = "testrg";
    var clusterName = "mystreamingcluster";
    var clusterUpdateParameters = new ClusterUpdate(enableStreamingIngest: true);

    await kustoManagementClient.Clusters.UpdateAsync(resourceGroupName, clusterName, clusterUpdateParameters);
```

> [!WARNING]
> 스트리밍 수집을 사용 하도록 설정 하기 전에 [제한 사항을](#limitations) 검토 합니다.

## <a name="create-a-target-table-and-define-the-policy-using-c"></a>대상 테이블을 만들고 C를 사용 하 여 정책을 정의 합니다. #

테이블을 만들고이 테이블에 대 한 스트리밍 수집 정책을 정의 하려면 다음 코드를 실행 합니다.

```csharp
    var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
    var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
    var clientSecret = "xxxxxxxxxxxxxx";//Client Secret
    var databaseName = "StreamingTestDb";
    var tableName = "TestTable";
    var kcsb = new KustoConnectionStringBuilder("https://mystreamingcluster.westcentralus.kusto.windows.net", databaseName);
    kcsb = kcsb.WithAadApplicationKeyAuthentication(clientId, clientSecret, tenantId);

    var tableSchema = new TableSchema(
        tableName,
        new ColumnSchema[]
        {
            new ColumnSchema("TimeStamp", "System.DateTime"),
            new ColumnSchema("Name",      "System.String"),
            new ColumnSchema("Metric",    "System.int"),
            new ColumnSchema("Source",    "System.String"),
        });

    var tableCreateCommand = CslCommandGenerator.GenerateTableCreateCommand(tableSchema);
    var tablePolicyAlterCommand = CslCommandGenerator.GenerateTableAlterStreamingIngestionPolicyCommand(tableName, isEnabled: true);
    using (var client = KustoClientFactory.CreateCslAdminProvider(kcsb))
    {
        client.ExecuteControlCommand(tableCreateCommand);

        client.ExecuteControlCommand(tablePolicyAlterCommand);
    }
```

[!INCLUDE [ingest-data-streaming-use](includes/ingest-data-streaming-types.md)]

[!INCLUDE [ingest-data-streaming-disable](includes/ingest-data-streaming-disable.md)]

### <a name="drop-the-streaming-ingestion-policy-using-c"></a>C를 사용 하 여 스트리밍 수집 정책 삭제 #

테이블에서 스트리밍 수집 정책을 삭제 하려면 다음 코드를 실행 합니다.
    
```csharp
        var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
        var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
        var clientSecret = "xxxxxxxxxxxxxx";//Client Secret
        var databaseName = "StreamingTestDb";
        var tableName = "TestTable";
        var kcsb = new KustoConnectionStringBuilder("https://mystreamingcluster.westcentralus.kusto.windows.net", databaseName);
        kcsb = kcsb.WithAadApplicationKeyAuthentication(clientId, clientSecret, tenantId);
    
        var tablePolicyDropCommand = CslCommandGenerator.GenerateTableStreamingIngestionPolicyDropCommand(databaseName, tableName);
        using (var client = KustoClientFactory.CreateCslAdminProvider(kcsb))
        {
            client.ExecuteControlCommand(tablePolicyDropCommand);
        }
```

### <a name="disable-streaming-ingestion-using-c"></a>C를 사용 하 여 스트리밍 수집 사용 안 함 #

클러스터에서 스트리밍 수집을 사용 하지 않도록 설정 하려면 다음 코드를 실행 합니다.
    
```csharp
        var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
        var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
        var clientSecret = "xxxxxxxxxxxxxx";//Client Secret
        var subscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";
        var authenticationContext = new AuthenticationContext($"https://login.windows.net/{tenantId}");
        var credential = new ClientCredential(clientId, clientSecret);
        var result = await authenticationContext.AcquireTokenAsync(resource: "https://management.core.windows.net/", clientCredential: credential);
    
        var credentials = new TokenCredentials(result.AccessToken, result.AccessTokenType);
    
        var kustoManagementClient = new KustoManagementClient(credentials)
        {
            SubscriptionId = subscriptionId
        };
    
        var resourceGroupName = "testrg";
        var clusterName = "mystreamingcluster";
        var clusterUpdateParameters = new ClusterUpdate(enableStreamingIngest: false);
    
        await kustoManagementClient.Clusters.UpdateAsync(resourceGroupName, clusterName, clusterUpdateParameters);
```
    
[!INCLUDE [ingest-data-streaming-limitations](includes/ingest-data-streaming-limitations.md)]

## <a name="next-steps"></a>다음 단계

* [Azure 데이터 탐색기에서 데이터 쿼리](web-query-data.md)
