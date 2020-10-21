---
title: 'C를 사용 하 여 Azure 데이터 탐색기 클러스터 & DB 만들기 #'
description: C#을 사용하여 Azure Data Explorer 클러스터 및 데이터베이스를 만드는 방법을 알아봅니다.
author: orspod
ms.author: orspodek
ms.reviewer: lugoldbe
ms.service: data-explorer
ms.topic: how-to
ms.date: 06/03/2019
ms.openlocfilehash: 444704ae7ff7554186f357de526a5dc11e79165d
ms.sourcegitcommit: 898f67b83ae8cf55e93ce172a6fd3473b7c1c094
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/21/2020
ms.locfileid: "92343116"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-c"></a>C#을 사용하여 Azure Data Explorer 클러스터 및 데이터베이스 만들기

> [!div class="op_single_selector"]
> * [포털](create-cluster-database-portal.md)
> * [CLI](create-cluster-database-cli.md)
> * [PowerShell](create-cluster-database-powershell.md)
> * [C#](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
> * [Azure Resource Manager 템플릿](create-cluster-database-resource-manager.md)

Azure Data Explorer는 애플리케이션, 웹 사이트, IoT 디바이스 등으로부터 대량의 데이터 스트리밍에 대한 실시간 분석을 제공하는 빠른 속도의 완전 관리형 데이터 분석 서비스입니다. Azure Data Explorer를 사용하려면 먼저 클러스터를 만들고 이 클러스터에 데이터베이스를 하나 이상 만듭니다. 그런 다음, 데이터베이스에 대해 쿼리를 실행할 수 있도록 데이터베이스에 데이터를 수집(로드)합니다. 이 문서에서는 c #을 사용 하 여 클러스터 및 데이터베이스를 만듭니다.

## <a name="prerequisites"></a>사전 요구 사항

* Visual Studio 2019가 설치되지 않은 경우 **체험판** [Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/)을 다운로드하고 사용할 수 있습니다. Visual Studio를 설치하는 동안 **Azure 개발**을 사용하도록 설정합니다.
* Azure 구독이 아직 없는 경우 시작하기 전에 [Azure 체험 계정](https://azure.microsoft.com/free/)을 만듭니다.

[!INCLUDE [data-explorer-data-connection-install-nuget-csharp](includes/data-explorer-data-connection-install-nuget-csharp.md)]

## <a name="authentication"></a>인증
이 문서의 예제를 실행 하려면 리소스에 액세스할 수 있는 Azure AD 응용 프로그램 및 서비스 주체가 필요 합니다. [AZURE ad 응용 프로그램 만들기](/azure/active-directory/develop/howto-create-service-principal-portal) 를 선택 하 여 무료 Azure Ad 응용 프로그램을 만들고 구독 범위에서 역할 할당을 추가 합니다. 또한, 및를 가져오는 방법을 보여 `Directory (tenant) ID` 줍니다 `Application ID` `Client Secret` .

## <a name="create-the-azure-data-explorer-cluster"></a>Azure Data Explorer 클러스터 만들기

1. 다음 코드를 사용하여 클러스터를 만듭니다.

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
    var clusterName = "mykustocluster";
    var location = "Central US";
    var skuName = "Standard_D13_v2";
    var tier = "Standard";
    var capacity = 5;
    var sku = new AzureSku(skuName, tier, capacity);
    var cluster = new Cluster(location, sku);
    await kustoManagementClient.Clusters.CreateOrUpdateAsync(resourceGroupName, clusterName, cluster);
    ```

   |**설정** | **제안 값** | **필드 설명**|
   |---|---|---|
   | clusterName | *mykustocluster* | 원하는 클러스터 이름입니다.|
   | skuName | *Standard_D13_v2* | 클러스터에 사용될 SKU입니다. |
   | 계층 | *표준* | SKU 계층입니다. |
   | 용량 | *number* | 클러스터의 인스턴스 수입니다. |
   | resourceGroupName | *testrg* | 클러스터가 만들어질 리소스 그룹 이름입니다. |

    > [!NOTE]
    > **클러스터 만들기** 는 장기 실행 작업 이므로 CreateOrUpdate 대신 CreateOrUpdateAsync를 사용 하는 것이 좋습니다. 

1. 다음 명령을 실행하여 클러스터가 성공적으로 만들어졌는지 확인합니다.

    ```csharp
    kustoManagementClient.Clusters.Get(resourceGroupName, clusterName);
    ```

결과에 값이 `Succeeded`인 `ProvisioningState`가 있으면 클러스터가 성공적으로 만들어진 것입니다.

## <a name="create-the-database-in-the-azure-data-explorer-cluster"></a>Azure Data Explorer 클러스터에 데이터베이스 만들기

1. 다음 코드를 사용하여 데이터베이스를 만듭니다.

    ```csharp
    var hotCachePeriod = new TimeSpan(3650, 0, 0, 0);
    var softDeletePeriod = new TimeSpan(3650, 0, 0, 0);
    var databaseName = "mykustodatabase";
    var database = new ReadWriteDatabase(location: location, softDeletePeriod: softDeletePeriod, hotCachePeriod: hotCachePeriod);

    await kustoManagementClient.Databases.CreateOrUpdateAsync(resourceGroupName, clusterName, databaseName, database);
    ```

    > [!NOTE]
    > C # 버전 2.0.0을 사용 하는 경우 ReadWriteDatabase 대신 데이터베이스를 사용 합니다.

   |**설정** | **제안 값** | **필드 설명**|
   |---|---|---|
   | clusterName | *mykustocluster* | 데이터베이스가 만들어지는 클러스터의 이름입니다.|
   | databaseName | *mykustodatabase* | 데이터베이스의 이름입니다.|
   | resourceGroupName | *testrg* | 클러스터가 만들어질 리소스 그룹 이름입니다. |
   | softDeletePeriod | *3650:00:00:00* | 데이터를 쿼리할 수 있도록 유지되는 시간입니다. |
   | hotCachePeriod | *3650:00:00:00* | 데이터가 캐시에 유지되는 시간입니다. |

2. 다음 명령을 실행하여 직접 만든 데이터베이스를 살펴봅니다.

    ```csharp
    kustoManagementClient.Databases.Get(resourceGroupName, clusterName, databaseName) as ReadWriteDatabase;
    ```

이제 클러스터와 데이터베이스가 있습니다.

## <a name="clean-up-resources"></a>리소스 정리

* 다른 문서를 따르려면 만든 리소스를 유지 합니다.
* 리소스를 정리하려면 클러스터를 삭제합니다. 클러스터를 삭제하면 해당 클러스터에 있는 모든 데이터베이스도 함께 삭제됩니다. 다음 명령을 사용하여 클러스터를 삭제합니다.

    ```csharp
    kustoManagementClient.Clusters.Delete(resourceGroupName, clusterName);
    ```

## <a name="next-steps"></a>다음 단계

* [Azure Data Explorer .NET Standard SDK(미리 보기)를 사용하여 데이터 수집](./net-sdk-ingest-data.md)