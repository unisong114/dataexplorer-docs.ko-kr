---
title: 'C를 사용 하 여 Azure 데이터 탐색기에 대 한 Event Grid 데이터 연결 만들기 #'
description: '이 문서에서는 c #을 사용 하 여 Azure 데이터 탐색기에 대 한 Event Grid 데이터 연결을 만드는 방법에 대해 알아봅니다.'
author: orspod
ms.author: orspodek
ms.reviewer: lugoldbe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/07/2019
ms.openlocfilehash: a93b29d6557afa435cacbbeeb43b685c0dfbf3af
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87350216"
---
# <a name="create-an-event-grid-data-connection-for-azure-data-explorer-by-using-c"></a>C를 사용 하 여 Azure 데이터 탐색기에 대 한 Event Grid 데이터 연결 만들기 #

> [!div class="op_single_selector"]
> * [포털](ingest-data-event-grid.md)
> * [C#](data-connection-event-grid-csharp.md)
> * [Python](data-connection-event-grid-python.md)
> * [Azure Resource Manager 템플릿](data-connection-event-grid-resource-manager.md)


Azure 데이터 탐색기는 로그 및 원격 분석 데이터에 사용 가능한 빠르고 확장성이 우수한 데이터 탐색 서비스입니다. Azure 데이터 탐색기는 blob 컨테이너에 기록 된 Event Hubs, IoT Hub 및 blob에서 수집 (데이터 로드)을 제공 합니다. 이 문서에서는 c #을 사용 하 여 Azure 데이터 탐색기에 대 한 Event Grid 데이터 연결을 만듭니다.

## <a name="prerequisites"></a>필수 구성 요소

* Visual Studio 2019가 설치되지 않은 경우 **체험판** [Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/)을 다운로드하고 사용할 수 있습니다. Visual Studio를 설치하는 동안 **Azure 개발**을 사용하도록 설정합니다.
* Azure 구독이 아직 없는 경우 시작하기 전에 [Azure 체험 계정](https://azure.microsoft.com/free/)을 만듭니다.
* [클러스터 및 데이터베이스](create-cluster-database-csharp.md) 만들기
* [테이블 및 열 매핑](net-standard-ingest-data.md#create-a-table-on-your-test-cluster) 만들기
* [데이터베이스 및 테이블 정책](database-table-policies-csharp.md) 설정 (선택 사항)
* [Event Grid 구독을 사용 하 여 저장소 계정을](../data-explorer/kusto/management/data-ingestion/eventgrid.md#create-an-event-grid-subscription-in-your-storage-account)만듭니다.

[!INCLUDE [data-explorer-data-connection-install-nuget-csharp](includes/data-explorer-data-connection-install-nuget-csharp.md)]

[!INCLUDE [data-explorer-authentication](includes/data-explorer-authentication.md)]

## <a name="add-an-event-grid-data-connection"></a>Event Grid 데이터 연결 추가

다음 예제에서는 프로그래밍 방식으로 Event Grid 데이터 연결을 추가 하는 방법을 보여 줍니다. Azure Portal를 사용 하 여 Event Grid 데이터 연결을 추가 하려면 [Azure 데이터 탐색기에서 Event Grid 데이터 연결 만들기](ingest-data-event-grid.md#create-an-event-grid-data-connection-in-azure-data-explorer) 를 참조 하세요.

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
//The cluster and database that are created as part of the Prerequisites
var clusterName = "mykustocluster";
var databaseName = "mykustodatabase";
var dataConnectionName = "myeventhubconnect";
//The event hub and storage account that are created as part of the Prerequisites
var eventHubResourceId = "/subscriptions/xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx/resourceGroups/xxxxxx/providers/Microsoft.EventHub/namespaces/xxxxxx/eventhubs/xxxxxx";
var storageAccountResourceId = "/subscriptions/xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx/resourceGroups/xxxxxx/providers/Microsoft.Storage/storageAccounts/xxxxxx";
var consumerGroup = "$Default";
var location = "Central US";
//The table and column mapping are created as part of the Prerequisites
var tableName = "StormEvents";
var mappingRuleName = "StormEvents_CSV_Mapping";
var dataFormat = DataFormat.CSV;

await kustoManagementClient.DataConnections.CreateOrUpdateAsync(resourceGroupName, clusterName, databaseName, dataConnectionName,
    new EventGridDataConnection(storageAccountResourceId, eventHubResourceId, consumerGroup, tableName: tableName, location: location, mappingRuleName: mappingRuleName, dataFormat: dataFormat));
```

|**설정** | **제안 값** | **필드 설명**|
|---|---|---|
| tenantId | *xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx-xxxxx-xxxxxxxxx* | 테넌트 ID 디렉터리 ID 라고도 합니다.|
| subscriptionId | *xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx-xxxxx-xxxxxxxxx* | 리소스를 만드는 데 사용 하는 구독 ID입니다.|
| clientId | *xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx-xxxxx-xxxxxxxxx* | 테 넌 트의 리소스에 액세스할 수 있는 응용 프로그램의 클라이언트 ID입니다.|
| clientSecret | *xxxxxxxxxxxxxx* | 테 넌 트의 리소스에 액세스할 수 있는 응용 프로그램의 클라이언트 암호입니다. |
| resourceGroupName | *testrg* | 클러스터를 포함 하는 리소스 그룹의 이름입니다.|
| clusterName | *mykustocluster* | 클러스터의 이름입니다.|
| databaseName | *mykustodatabase* | 클러스터에 있는 대상 데이터베이스의 이름입니다.|
| dataConnectionName | *myeventhubconnect* | 원하는 데이터 연결 이름입니다.|
| tableName | *StormEvents* | 대상 데이터베이스에 있는 대상 테이블의 이름입니다.|
| mappingRuleName | *StormEvents_CSV_Mapping* | 대상 테이블과 관련 된 열 매핑의 이름입니다.|
| dataFormat | *csv* | 메시지의 데이터 형식입니다.|
| eventHubResourceId | *리소스 ID* | 이벤트를 보내도록 Event Grid 구성 된 이벤트 허브의 리소스 ID입니다. |
| storageAccountResourceId | *리소스 ID* | 수집할 데이터를 보유 하는 저장소 계정의 리소스 ID입니다. |
| consumerGroup | *$Default* | 이벤트 허브의 소비자 그룹입니다.|
| 위치 | *미국 중부* | 데이터 연결 리소스의 위치입니다.|

## <a name="generate-sample-data"></a>샘플 데이터 생성

이제 Azure 데이터 탐색기와 저장소 계정이 연결 되었으므로 샘플 데이터를 만들어 저장소에 업로드할 수 있습니다.

> [!NOTE]
> Azure 데이터 탐색기는 blob 사후 수집을 삭제 하지 않습니다. Blob 삭제를 관리 하려면 [Azure blob 저장소 수명 주기](/azure/storage/blobs/storage-lifecycle-management-concepts?tabs=azure-portal) 를 사용 하 여 3 ~ 5 일간 blob을 유지 합니다.

### <a name="upload-file-using-azure-blob-storage-sdk"></a>Azure Blob Storage SDK를 사용 하 여 파일 업로드

다음 코드 조각에서는 저장소 계정에 새 컨테이너를 만들고, 기존 파일 (blob)을 해당 컨테이너에 업로드 한 다음, 컨테이너의 blob을 나열 합니다.

```csharp
var azureStorageAccountConnectionString=<storage_account_connection_string>;

var containerName = <container_name>;
var blobName = <blob_name>;
var localFileName = <file_to_upload>;
var uncompressedSizeInBytes = <uncompressed_size_in_bytes>;
var mapping = <mappingReference>;

// Creating the container
var azureStorageAccount = CloudStorageAccount.Parse(azureStorageAccountConnectionString);
var blobClient = azureStorageAccount.CreateCloudBlobClient();
var container = blobClient.GetContainerReference(containerName);
container.CreateIfNotExists();

// Set metadata and upload file to blob
var blob = container.GetBlockBlobReference(blobName);
blob.Metadata.Add("rawSizeBytes", uncompressedSizeInBytes);
blob.Metadata.Add("kustoIngestionMappingReference", mapping);
blob.UploadFromFile(localFileName);

// List blobs
var blobs = container.ListBlobs();
```

### <a name="upload-file-using-azure-data-lake-sdk"></a>Azure Data Lake SDK를 사용 하 여 파일 업로드

Data Lake Storage Gen2 작업할 때 [AZURE DATA LAKE SDK](https://www.nuget.org/packages/Azure.Storage.Files.DataLake/) 를 사용 하 여 저장소에 파일을 업로드할 수 있습니다. 다음 코드 조각은 Azure Data Lake 저장소에 새 파일 시스템을 만들고 해당 파일 시스템에 메타 데이터가 포함 된 로컬 파일을 업로드 합니다.

```csharp
var accountName = <storage_account_name>;
var accountKey = <storage_account_key>;
var fileSystemName = <file_system_name>;
var fileName = <file_name>;
var localFileName = <file_to_upload>;
var uncompressedSizeInBytes = <uncompressed_size_in_bytes>;
var mapping = <mapping_reference>;

var sharedKeyCredential = new StorageSharedKeyCredential(accountName, accountKey);
var dfsUri = "https://" + accountName + ".dfs.core.windows.net";
var dataLakeServiceClient = new DataLakeServiceClient(new Uri(dfsUri), sharedKeyCredential);

// Create the filesystem and an empty file
var dataLakeFileSystemClient = dataLakeServiceClient.CreateFileSystem(fileSystemName).Value;
var dataLakeFileClient = dataLakeFileSystemClient.CreateFile(fileName).Value;

// Set metadata
IDictionary<String, String> metadata = new Dictionary<string, string>();
metadata.Add("rawSizeBytes", uncompressedSizeInBytes);
metadata.Add("kustoIngestionMappingReference", mapping);
dataLakeFileClient.SetMetadata(metadata);

// Write to the file and close it
var fileStream = File.OpenRead(localFileName);
var fileSize = fileStream.Length;
dataLakeFileClient.Append(fileStream, offset: 0);
dataLakeFileClient.Flush(position: fileSize, close: true); // Note: This line triggers the event being processed by the data connection
```

> [!NOTE]
> [AZURE DATA LAKE SDK](https://www.nuget.org/packages/Azure.Storage.Files.DataLake/) 를 사용 하 여 파일을 업로드 하는 경우 [CreateFile](/dotnet/api/azure.storage.files.datalake.datalakefilesystemclient.createfile?view=azure-dotnet) 에 대 한 첫 번째 호출에서 크기가 0 인 Event Grid 이벤트를 트리거하고이 이벤트는 Azure 데이터 탐색기에서 무시 됩니다. "Close" 매개 변수를 "true"로 설정 하 여 flush를 호출할 때 다른 이벤트가 트리거됩니다. 이 이벤트는이 업데이트가 최종 업데이트이 고 파일 스트림이 닫 혔 음을 나타냅니다. 이 이벤트는 Event Grid 데이터 연결에 의해 처리 됩니다. 플러시하는 방법에 대 한 자세한 내용은 [Azure Data Lake flush 메서드](/dotnet/api/azure.storage.files.datalake.datalakefileclient.flush?view=azure-dotnet)를 참조 하세요.

[!INCLUDE [data-explorer-data-connection-clean-resources-csharp](includes/data-explorer-data-connection-clean-resources-csharp.md)]
