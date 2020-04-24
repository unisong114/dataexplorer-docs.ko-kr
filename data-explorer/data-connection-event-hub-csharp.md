---
title: 'C를 사용하여 Azure 데이터 탐색기의 이벤트 허브 데이터 연결 만들기 #'
description: 이 문서에서는 C#을 사용하여 Azure 데이터 탐색기의 이벤트 허브 데이터 연결을 만드는 방법을 알아봅니다.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/07/2019
ms.openlocfilehash: 54f7541321b50ae4d3cf679d9224f70cb21a46ab
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81497773"
---
# <a name="create-an-event-hub-data-connection-for-azure-data-explorer-by-using-c"></a>C를 사용하여 Azure 데이터 탐색기의 이벤트 허브 데이터 연결 만들기 #

> [!div class="op_single_selector"]
> * [포털](ingest-data-event-hub.md)
> * [C#](data-connection-event-hub-csharp.md)
> * [Python](data-connection-event-hub-python.md)
> * [Azure Resource Manager 템플릿](data-connection-event-hub-resource-manager.md)

Azure 데이터 탐색기는 로그 및 원격 분석 데이터에 사용 가능한 빠르고 확장성이 우수한 데이터 탐색 서비스입니다. Azure Data Explorer는 이벤트 허브, IoT 허브 및 Blob 컨테이너에 기록된 Blob에서 수집(데이터 로드)을 제공합니다. 이 문서에서는 C#을 사용하여 Azure 데이터 탐색기의 이벤트 허브 데이터 연결을 만듭니다.

## <a name="prerequisites"></a>사전 요구 사항

* Visual Studio 2019가 설치되지 않은 경우 **체험판** [Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/)을 다운로드하고 사용할 수 있습니다. Visual Studio 설정 중에 **Azure 개발을** 사용하도록 설정해야 합니다.
* Azure 구독이 아직 없는 경우 시작하기 전에 [Azure 체험 계정](https://azure.microsoft.com/free/)을 만듭니다.
* [클러스터 및 데이터베이스](create-cluster-database-csharp.md) 만들기
* [테이블 및 열 매핑](net-standard-ingest-data.md#create-a-table-on-your-test-cluster) 만들기
* [데이터베이스 및 테이블 정책](database-table-policies-csharp.md) 설정(선택 사항)
* [수집을 위한 데이터가 있는 이벤트 허브를](ingest-data-event-hub.md#create-an-event-hub)만듭니다. 

[!INCLUDE [data-explorer-data-connection-install-nuget-csharp](includes/data-explorer-data-connection-install-nuget-csharp.md)]

[!INCLUDE [data-explorer-authentication](includes/data-explorer-authentication.md)]

## <a name="add-an-event-hub-data-connection"></a>이벤트 허브 데이터 연결 추가

다음 예제에서는 이벤트 허브 데이터 연결을 프로그래밍 방식으로 추가하는 방법을 보여 주며, Azure 포털을 사용하여 이벤트 허브 데이터 연결을 추가하려면 [이벤트 허브에 연결합니다.](ingest-data-event-hub.md#connect-to-the-event-hub)

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
//The event hub that is created as part of the Prerequisites
var eventHubResourceId = "/subscriptions/xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx/resourceGroups/xxxxxx/providers/Microsoft.EventHub/namespaces/xxxxxx/eventhubs/xxxxxx";
var consumerGroup = "$Default";
var location = "Central US";
//The table and column mapping are created as part of the Prerequisites
var tableName = "StormEvents";
var mappingRuleName = "StormEvents_CSV_Mapping";
var dataFormat = DataFormat.CSV;
await kustoManagementClient.DataConnections.CreateOrUpdateAsync(resourceGroupName, clusterName, databaseName, dataConnectionName, 
    new EventHubDataConnection(eventHubResourceId, consumerGroup, location: location, tableName: tableName, mappingRuleName: mappingRuleName, dataFormat: dataFormat));
```

|**설정** | **제안 값** | **필드 설명**|
|---|---|---|
| tenantId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx* | 테넌트 ID 디렉터리 ID라고도 합니다.|
| subscriptionId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx* | 리소스 만들기에 사용하는 구독 ID입니다.|
| clientId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx* | 테넌트의 리소스에 액세스할 수 있는 응용 프로그램의 클라이언트 ID입니다.|
| clientSecret | *트리플 엑스 트리플 엑스 트리플 엑스 트리플 엑스* | 테넌트의 리소스에 액세스할 수 있는 응용 프로그램의 클라이언트 보안 입니다.|
| resourceGroupName | *테서그 (것)에 대한* | 클러스터를 포함하는 리소스 그룹의 이름입니다.|
| clusterName | *mykustocluster* | 클러스터의 이름입니다.|
| databaseName | *mykustodatabase* | 클러스터의 대상 데이터베이스 이름입니다.|
| 데이터연결 이름 | *마이이벤트허브커넥트* | 데이터 연결의 원하는 이름입니다.|
| tableName | *스톰 이벤트* | 대상 데이터베이스의 대상 테이블 이름입니다.|
| 매핑규칙 이름 | *StormEvents_CSV_Mapping* | 대상 테이블과 관련된 열 매핑의 이름입니다.|
| 데이터 형식 | *Csv* | 메시지의 데이터 형식입니다.|
| 이벤트허브리소스ID | *리소스 ID* | 수집을 위한 데이터를 보유하는 이벤트 허브의 리소스 ID입니다. |
| 소비자 그룹 | *$Default* | 이벤트 허브의 소비자 그룹입니다.|
| 위치 | *미국 중부* | 데이터 연결 리소스의 위치입니다.|

[!INCLUDE [data-explorer-data-connection-clean-resources-csharp](includes/data-explorer-data-connection-clean-resources-csharp.md)]
