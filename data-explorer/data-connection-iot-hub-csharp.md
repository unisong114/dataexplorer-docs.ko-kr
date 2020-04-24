---
title: 'C를 사용하여 Azure 데이터 탐색기용 IoT Hub 데이터 연결 만들기 #'
description: 이 문서에서는 C#을 사용하여 Azure 데이터 탐색기용 IoT Hub 데이터 연결을 만드는 방법을 알아봅니다.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/07/2019
ms.openlocfilehash: 1b2c74155ba1cb26cb6dd9ca40f9f8c0deda2646
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81497721"
---
# <a name="create-an-iot-hub-data-connection-for-azure-data-explorer-by-using-c-preview"></a>C#(미리 보기)를 사용하여 Azure 데이터 탐색기용 IoT Hub 데이터 연결 만들기

> [!div class="op_single_selector"]
> * [포털](ingest-data-iot-hub.md)
> * [C#](data-connection-iot-hub-csharp.md)
> * [Python](data-connection-iot-hub-python.md)
> * [Azure Resource Manager 템플릿](data-connection-iot-hub-resource-manager.md)

Azure 데이터 탐색기는 로그 및 원격 분석 데이터에 사용 가능한 빠르고 확장성이 우수한 데이터 탐색 서비스입니다. Azure Data Explorer는 이벤트 허브, IoT 허브 및 Blob 컨테이너에 기록된 Blob에서 수집(데이터 로드)을 제공합니다. 이 문서에서는 C#을 사용하여 Azure 데이터 탐색기용 IoT Hub 데이터 연결을 만듭니다.

## <a name="prerequisites"></a>사전 요구 사항

* Visual Studio 2019가 설치되지 않은 경우 **체험판** [Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/)을 다운로드하고 사용할 수 있습니다. Visual Studio 설정 중에 **Azure 개발을** 사용하도록 설정해야 합니다.
* Azure 구독이 아직 없는 경우 시작하기 전에 [Azure 체험 계정](https://azure.microsoft.com/free/)을 만듭니다.
* [클러스터 및 데이터베이스](create-cluster-database-csharp.md) 만들기
* [테이블 및 열 매핑](net-standard-ingest-data.md#create-a-table-on-your-test-cluster) 만들기
* [데이터베이스 및 테이블 정책](database-table-policies-csharp.md) 설정(선택 사항)
* 을 [구성한 공유 액세스 정책으로 IoT Hub를](ingest-data-iot-hub.md#create-an-iot-hub)만듭니다.

[!INCLUDE [data-explorer-data-connection-install-nuget-csharp](includes/data-explorer-data-connection-install-nuget-csharp.md)]

[!INCLUDE [data-explorer-authentication](includes/data-explorer-authentication.md)]

## <a name="add-an-iot-hub-data-connection"></a>IoT 허브 데이터 연결 추가 

다음 예제에서는 프로그래밍 방식으로 IoT Hub 데이터 연결을 추가하는 방법을 보여 주며, 이 예제에서는 IoT Hub 데이터 연결을 프로그래밍 방식으로 추가하는 방법을 보여 주어 도처에서 보여 주실 수 있습니다. Azure 포털을 사용하여 Iot Hub 데이터 연결을 추가하려면 [Azure 데이터 탐색기 테이블을 IoT Hub에 연결합니다.](ingest-data-iot-hub.md#connect-azure-data-explorer-table-to-iot-hub)

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
//The IoT hub that is created as part of the Prerequisites
var iotHubResourceId = "/subscriptions/xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx/resourceGroups/xxxxxx/providers/Microsoft.Devices/IotHubs/xxxxxx";
var sharedAccessPolicyName = "iothubforread";
var consumerGroup = "$Default";
var location = "Central US";
//The table and column mapping are created as part of the Prerequisites
var tableName = "StormEvents";
var mappingRuleName = "StormEvents_CSV_Mapping";
var dataFormat = DataFormat.CSV;

await kustoManagementClient.DataConnections.CreateOrUpdate(resourceGroupName, clusterName, databaseName, dataConnectionName,
            new IotHubDataConnection(iotHubResourceId, consumerGroup, sharedAccessPolicyName, tableName: tableName, location: location, mappingRuleName: mappingRuleName, dataFormat: dataFormat));
```

|**설정** | **제안 값** | **필드 설명**|
|---|---|---|
| tenantId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx* | 테넌트 ID 디렉터리 ID라고도 합니다.|
| subscriptionId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx* | 리소스 만들기에 사용하는 구독 ID입니다.|
| clientId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx* | 테넌트의 리소스에 액세스할 수 있는 응용 프로그램의 클라이언트 ID입니다.|
| clientSecret | *트리플 엑스 트리플 엑스 트리플 엑스 트리플 엑스* | 테넌트의 리소스에 액세스할 수 있는 응용 프로그램의 클라이언트 보안 입니다. |
| resourceGroupName | *테서그 (것)에 대한* | 클러스터를 포함하는 리소스 그룹의 이름입니다.|
| clusterName | *mykustocluster* | 클러스터의 이름입니다.|
| databaseName | *mykustodatabase* | 클러스터의 대상 데이터베이스 이름입니다.|
| 데이터연결 이름 | *마이이벤트허브커넥트* | 데이터 연결의 원하는 이름입니다.|
| tableName | *스톰 이벤트* | 대상 데이터베이스의 대상 테이블 이름입니다.|
| 매핑규칙 이름 | *StormEvents_CSV_Mapping* | 대상 테이블과 관련된 열 매핑의 이름입니다.|
| 데이터 형식 | *Csv* | 메시지의 데이터 형식입니다.|
| iotHubResourceId | *리소스 ID* | 수집을 위한 데이터를 보유하는 IoT 허브의 리소스 ID입니다. |
| 공유 액세스 정책 이름 | *이오허브포레드* | IoT Hub에 연결할 장치 및 서비스에 대한 권한을 정의하는 공유 액세스 정책의 이름입니다. |
| 소비자 그룹 | *$Default* | 이벤트 허브의 소비자 그룹입니다.|
| 위치 | *미국 중부* | 데이터 연결 리소스의 위치입니다.|

[!INCLUDE [data-explorer-data-connection-clean-resources-csharp](includes/data-explorer-data-connection-clean-resources-csharp.md)]
