---
title: 'C를 통해 Azure 데이터 탐색기로의 종단 간 Blob 수집 #'
description: 이 문서에서는 C#을 사용하는 종단 간 예제를 사용하여 Blob을 Azure Data 탐색기로 통합하는 방법을 알아봅니다.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 02/03/2020
ms.openlocfilehash: 8df2202d3a127c58cee90353a628bf933ea30a10
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81499255"
---
# <a name="end-to-end-blob-ingestion-into-azure-data-explorer-through-c"></a>C를 통해 Azure 데이터 탐색기로의 종단 간 Blob 수집 #

> [!div class="op_single_selector"]
> * [C#](end-to-end-csharp.md)
> * [Python](end-to-end-python.md)
>

Azure Data Explorer는 로그 및 원격 분석 데이터에 사용 가능한 빠르고 확장이 가능한 데이터 탐색 서비스로서, 이 문서에서는 Azure Blob 저장소에서 Azure 데이터 탐색기로 데이터를 통합하는 방법에 대한 종단 간 예제를 제공합니다. 

리소스 그룹, 저장소 계정 및 컨테이너, 이벤트 허브 및 Azure Data Explorer 클러스터 및 데이터베이스를 프로그래밍 방식으로 만드는 방법을 알아봅니다. 새 저장소 계정에서 데이터를 수집하도록 Azure Data Explorer를 프로그래밍 방식으로 구성하는 방법도 알아봅니다.

## <a name="prerequisites"></a>사전 요구 사항

Azure 구독이 아직 없는 경우 시작하기 전에 [Azure 체험 계정](https://azure.microsoft.com/free/)을 만듭니다.

## <a name="install-c-nuget"></a>C# NuGet 설치

* [설치 마이크로 소프트.Azure.Management.kusto](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/).
* [설치 Microsoft.Azure.Management.ResourceManager.](https://www.nuget.org/packages/Microsoft.Azure.Management.ResourceManager)
* [설치 Microsoft.Azure.Management.EventGrid.](https://www.nuget.org/packages/Microsoft.Azure.Management.EventGrid/)
* [설치 마이크로 소프트.Azure.Storage.Blob](https://www.nuget.org/packages/Microsoft.Azure.Storage.Blob/).
* 인증을 위해 [Microsoft.Rest.ClientRuntime.Azure.인증을](https://www.nuget.org/packages/Microsoft.Rest.ClientRuntime.Azure.Authentication) 설치합니다.

[!INCLUDE [data-explorer-authentication](includes/data-explorer-authentication.md)]

[!INCLUDE [data-explorer-e2e-event-grid-resource-template](includes/data-explorer-e2e-event-grid-resource-template.md)]

## <a name="code-example"></a>코드 예제 

다음 코드 예제에서는 Azure 데이터 탐색기로 데이터를 수집하는 단계별 프로세스를 제공합니다. 

먼저 리소스 그룹을 만듭니다. 또한 저장소 계정 및 컨테이너, 이벤트 허브 및 Azure Data Explorer 클러스터 및 데이터베이스와 같은 Azure 리소스를 만들고 보안 주체를 추가합니다. 그런 다음 Azure 데이터 탐색기 데이터베이스에서 테이블 및 열 매핑과 함께 Azure Event Grid 구독을 만듭니다. 마지막으로 새 저장소 계정에서 데이터를 수집하도록 Azure Data Explorer를 구성하도록 데이터 연결을 만듭니다. 

```csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client secret
var subscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";
string location = "West Europe";
string locationSmallCase = "westeurope";
string azureResourceTemplatePath = @"xxxxxxxxx\template.json";//Path to the Azure Resource Manager template JSON from the previous section

string deploymentName = "e2eexample";
string resourceGroupName = deploymentName + "resourcegroup";
string eventHubName = deploymentName + "eventhub";
string eventHubNamespaceName = eventHubName + "ns";
string storageAccountName = deploymentName + "storage";
string storageContainerName = deploymentName + "storagecontainer";
string eventGridSubscriptionName = deploymentName + "eventgrid";
string kustoClusterName = deploymentName + "kustocluster";
string kustoDatabaseName = deploymentName + "kustodatabase";
string kustoTableName = "Events";
string kustoColumnMappingName = "Events_CSV_Mapping";
string kustoDataConnectionName = deploymentName + "kustoeventgridconnection";

//principals
string principalIdForCluster = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
string roleForClusterPrincipal = "AllDatabasesAdmin";
string tenantIdForClusterPrincipal = tenantId;
string principalTypeForCluster = "App";
string principalIdForDatabase = "xxxxxxxx@xxxxxxxx.com";//User Email
string roleForDatabasePrincipal = "Admin";
string tenantIdForDatabasePrincipal = tenantId;
string principalTypeForDatabase = "User";

var serviceCreds = await ApplicationTokenProvider.LoginSilentAsync(tenantId, clientId, clientSecret);
var resourceManagementClient = new ResourceManagementClient(serviceCreds);
Console.WriteLine("Step 1: Create a new resource group in your Azure subscription to manage all the resources for using Azure Data Explorer.");
resourceManagementClient.SubscriptionId = subscriptionId;
await resourceManagementClient.ResourceGroups.CreateOrUpdateAsync(resourceGroupName,
    new ResourceGroup() { Location = locationSmallCase });

Console.WriteLine(
    "Step 2: Create a Blob Storage, a container in the Storage account, an Event Hub, an Azure Data Explorer cluster, database, and add principals by using an Azure Resource Manager template.");
var parameters = new Dictionary<string, Dictionary<string, object>>();
parameters["eventHubNamespaceName"] = new Dictionary<string, object>(capacity: 1) {{"value", eventHubNamespaceName}};
parameters["eventHubName"] = new Dictionary<string, object>(capacity: 1) {{"value", eventHubName }};
parameters["storageAccountName"] = new Dictionary<string, object>(capacity: 1) {{"value", storageAccountName }};
parameters["containerName"] = new Dictionary<string, object>(capacity: 1) {{"value", storageContainerName }};
parameters["kustoClusterName"] = new Dictionary<string, object>(capacity: 1) {{"value", kustoClusterName }};
parameters["kustoDatabaseName"] = new Dictionary<string, object>(capacity: 1) {{"value", kustoDatabaseName }};
parameters["principalIdForCluster"] = new Dictionary<string, object>(capacity: 1) {{"value", principalIdForCluster }};
parameters["roleForClusterPrincipal"] = new Dictionary<string, object>(capacity: 1) {{"value", roleForClusterPrincipal }};
parameters["tenantIdForClusterPrincipal"] = new Dictionary<string, object>(capacity: 1) {{"value", tenantIdForClusterPrincipal }};
parameters["principalTypeForCluster"] = new Dictionary<string, object>(capacity: 1) {{"value", principalTypeForCluster }};
parameters["principalIdForDatabase"] = new Dictionary<string, object>(capacity: 1) {{"value", principalIdForDatabase }};
parameters["roleForDatabasePrincipal"] = new Dictionary<string, object>(capacity: 1) {{"value", roleForDatabasePrincipal }};
parameters["tenantIdForDatabasePrincipal"] = new Dictionary<string, object>(capacity: 1) {{"value", tenantIdForDatabasePrincipal }};
parameters["principalTypeForDatabase"] = new Dictionary<string, object>(capacity: 1) {{"value", principalTypeForDatabase }};
            
string template = File.ReadAllText(azureResourceTemplatePath, Encoding.UTF8);
await resourceManagementClient.Deployments.CreateOrUpdateAsync(resourceGroupName, deploymentName,
    new Deployment(new DeploymentProperties(DeploymentMode.Incremental, template: template,
        parameters: parameters)));

Console.WriteLine(
    "Step 3: Create an Event Grid subscription to publish blob events created in a specific container to an Event Hub.");
var eventGridClient = new EventGridManagementClient(serviceCreds)
{
    SubscriptionId = subscriptionId
};
string storageResourceId = $"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}";
string eventHubResourceId = $"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.EventHub/namespaces/{eventHubNamespaceName}/eventhubs/{eventHubName}";
await eventGridClient.EventSubscriptions.CreateOrUpdateAsync(storageResourceId, eventGridSubscriptionName,
    new EventSubscription()
    {
        Destination = new EventHubEventSubscriptionDestination(eventHubResourceId),
        Filter = new EventSubscriptionFilter()
        {
            SubjectBeginsWith = $"/blobServices/default/containers/{storageContainerName}",
            IncludedEventTypes = new List<string>(){"Microsoft.Storage.BlobCreated"}
        }
    });

Console.WriteLine("Step 4: Create a table (with three columns: EventTime, EventId, and EventSummary) and column mapping in your Azure Data Explorer database.");
var kustoUri = $"https://{kustoClusterName}.{locationSmallCase}.kusto.windows.net";
var kustoConnectionStringBuilder = new KustoConnectionStringBuilder(kustoUri)
{
    InitialCatalog = kustoDatabaseName,
    FederatedSecurity = true,
    ApplicationClientId = clientId,
    ApplicationKey = clientSecret,
    Authority = tenantId
};

using (var kustoClient = KustoClientFactory.CreateCslAdminProvider(kustoConnectionStringBuilder))
{
    var command =
        CslCommandGenerator.GenerateTableCreateCommand(
            kustoTableName,
            new[]
            {
                Tuple.Create("EventTime", "System.DateTime"),
                Tuple.Create("EventId", "System.Int32"),
                Tuple.Create("EventSummary", "System.String"),
            });

    kustoClient.ExecuteControlCommand(command);

    command = CslCommandGenerator.GenerateTableCsvMappingCreateCommand(
        kustoTableName,
        kustoColumnMappingName,
        new[]
        {
            new CsvColumnMapping { ColumnName = "EventTime", CslDataType="dateTime", Ordinal = 0 },
            new CsvColumnMapping { ColumnName = "EventId", CslDataType="int", Ordinal = 1 },
            new CsvColumnMapping { ColumnName = "EventSummary", CslDataType="string", Ordinal = 2 },
        });
    kustoClient.ExecuteControlCommand(command);
}

Console.WriteLine("Step 5: Add an Event Grid data connection. Azure Data Explorer will automatically ingest the data when new blobs are created.");
var kustoManagementClient = new KustoManagementClient(serviceCreds)
{
    SubscriptionId = subscriptionId
};
await kustoManagementClient.DataConnections.CreateOrUpdateAsync(resourceGroupName, kustoClusterName,
                kustoDatabaseName, dataConnectionName: kustoDataConnectionName, new EventGridDataConnection(storageResourceId, eventHubResourceId, consumerGroup: "$Default", location: location, tableName:kustoTableName, mappingRuleName: kustoColumnMappingName, dataFormat: "csv"));

```
| **설정** | **필드 설명** |
|---|---|---|
| tenantId | 테넌트 ID 디렉터리 ID라고도 합니다.|
| subscriptionId | 리소스 만들기에 사용하는 구독 ID입니다.|
| clientId | 테넌트의 리소스에 액세스할 수 있는 응용 프로그램의 클라이언트 ID입니다.|
| clientSecret | 테넌트의 리소스에 액세스할 수 있는 응용 프로그램의 클라이언트 보안 입니다. |

## <a name="test-the-code-example"></a>코드 예제 테스트

1. 저장소 계정에 파일을 업로드합니다.

    ```csharp
    string storageConnectionString = "DefaultEndpointsProtocol=https;AccountName=xxxxxxxxxxxxxx;AccountKey=xxxxxxxxxxxxxx;EndpointSuffix=core.windows.net";
    var cloudStorageAccount = CloudStorageAccount.Parse(storageConnectionString);
    CloudBlobClient blobClient = cloudStorageAccount.CreateCloudBlobClient();
    CloudBlobContainer container = blobClient.GetContainerReference(storageContainerName);
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("test.csv");
    var blobContent = @"2007-01-01 00:00:00.0000000,2592,Several trees down
    2007-01-01 00:00:00.0000000,4171,Winter Storm";
    await blockBlob.UploadTextAsync(blobContent);
    ```
    |**설정** | **필드 설명**|
    |---|---|---|
    | 스토리지연결스트링 | 프로그래밍 방식으로 만든 저장소 계정의 연결 문자열입니다.|

2. Azure 데이터 탐색기에서 테스트 쿼리를 실행합니다.

    ```csharp
    var kustoUri = $"https://{kustoClusterName}.{locationSmallCase}.kusto.windows.net";
    var kustoConnectionStringBuilder = new KustoConnectionStringBuilder(kustoUri)
    {
        InitialCatalog = kustoDatabaseName,
        FederatedSecurity = true,
        ApplicationClientId = clientId,
        ApplicationKey = clientSecret,
        Authority = tenantId
    };
    using (var kustoClient = KustoClientFactory.CreateCslQueryProvider(kustoConnectionStringBuilder))
    {
        var query = $"{kustoTableName} | take 10";
        using (var reader = kustoClient.ExecuteQuery(query) as DataTableReader2)
        {// Print the contents of each of the result sets. 
            while (reader.Read())
            {
                Console.WriteLine($"{reader[0]}, {reader[1]}, {reader[2]}");
            }
        }
    }
    ```

## <a name="clean-up-resources"></a>리소스 정리

리소스 그룹을 삭제하고 리소스를 정리하려면 다음 명령을 사용합니다.

```csharp
await resourceManagementClient.ResourceGroups.DeleteAsync(resourceGroupName);
```

## <a name="next-steps"></a>다음 단계

*  클러스터 및 데이터베이스를 만드는 다른 방법에 대해 알아보려면 [Azure Data Explorer 클러스터 및 데이터베이스 만들기](create-cluster-database-csharp.md)를 참조하십시오.
* 수집 방법에 대한 자세한 내용은 [Azure 데이터 탐색기 데이터 수집](ingest-data-overview.md)을 참조하십시오.
* 웹 응용 프로그램에 대해 자세히 알아보려면 [빠른 시작: Azure 데이터 탐색기 웹 UI의 쿼리 데이터를](web-query-data.md)참조하십시오.
* Kusto 쿼리 언어를 사용하여 [쿼리를 작성](write-queries.md)합니다.