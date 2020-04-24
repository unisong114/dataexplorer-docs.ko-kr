---
title: 팔로우 데이터베이스 기능을 사용하여 Azure 데이터 탐색기에서 데이터베이스 연결
description: 팔로워 데이터베이스 기능을 사용하여 Azure Data Explorer에서 데이터베이스를 연결하는 방법에 대해 알아봅니다.
author: orspod
ms.author: orspodek
ms.reviewer: gabilehner
ms.service: data-explorer
ms.topic: conceptual
ms.date: 11/07/2019
ms.openlocfilehash: 4c385551f8983219dddf840f7a2d590809b030a3
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81496720"
---
# <a name="use-follower-database-to-attach-databases-in-azure-data-explorer"></a>팔로워 데이터베이스를 사용하여 Azure 데이터 탐색기에서 데이터베이스 연결

**추종자 데이터베이스** 기능을 사용하면 다른 클러스터에 있는 데이터베이스를 Azure Data Explorer 클러스터에 연결할 수 있습니다. **추종자 데이터베이스는** 읽기 *전용* 모드로 연결되어 있으므로 데이터를 보고 **지시선 데이터베이스로**수집된 데이터에 대한 쿼리를 실행할 수 있습니다. 추종자 데이터베이스는 지시선 데이터베이스의 변경 내용을 동기화합니다. 동기화로 인해 데이터 가용성에 몇 초에서 몇 분의 데이터 지연이 있습니다. 지연 시간은 지시선 데이터베이스 메타데이터의 전체 크기에 따라 달라집니다. 리더 및 추종자 데이터베이스는 동일한 저장소 계정을 사용하여 데이터를 가져옵니다. 저장소는 리더 데이터베이스가 소유합니다. 추종자 데이터베이스는 데이터를 수집할 필요 없이 데이터를 볼 수 있습니다. 연결된 데이터베이스는 읽기 전용 데이터베이스이므로 데이터베이스의 데이터, 테이블 및 정책은 [캐싱 정책,](#configure-caching-policy)보안 [주체](#manage-principals)및 사용 권한을 제외하고 수정할 수 [없습니다.](#manage-permissions) 연결된 데이터베이스는 삭제할 수 없습니다. 리더 나 추종자에 의해 분리되어야하며 삭제 할 수 있습니다. 

팔로워 기능을 사용하여 다른 클러스터에 데이터베이스를 연결하는 것은 조직과 팀 간의 데이터를 공유하는 인프라로 사용됩니다. 이 기능은 컴퓨팅 리소스를 분리하여 프로덕션 환경을 비프로덕션 사용 사례로부터 보호하는 데 유용합니다. 팔로워를 사용하여 Azure Data Explorer 클러스터의 비용을 데이터에 대한 쿼리를 실행하는 당사자에 연결할 수도 있습니다.

## <a name="which-databases-are-followed"></a>어떤 데이터베이스를 따릅니까?

* 클러스터는 하나의 데이터베이스, 여러 데이터베이스 또는 리더 클러스터의 모든 데이터베이스를 따를 수 있습니다. 
* 단일 클러스터는 여러 리더 클러스터의 데이터베이스를 따를 수 있습니다. 
* 클러스터에는 팔로워 데이터베이스와 리더 데이터베이스가 모두 포함될 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

1. Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.
1. 리더 및 팔로워를 위한 [클러스터 및 DB를 만듭니다.](/azure/data-explorer/create-cluster-database-portal)
1. [수집 개요에서](/azure/data-explorer/ingest-data-overview)설명하는 다양한 방법 중 하나를 사용하여 리더 데이터베이스에 [데이터를 수집합니다.](/azure/data-explorer/ingest-sample-data)

## <a name="attach-a-database"></a>데이터베이스 연결

데이터베이스를 연결하는 데 사용할 수 있는 다양한 방법이 있습니다. 이 문서에서는 C# 또는 Azure 리소스 관리자 템플릿을 사용하여 데이터베이스를 연결하는 것에 대해 설명합니다. 데이터베이스를 연결하려면 리더 클러스터및 팔로워 클러스터에 대한 사용 권한이 있어야 합니다. 사용 권한에 대한 자세한 내용은 [권한 관리를](#manage-permissions)참조하십시오.

### <a name="attach-a-database-using-c"></a>C를 사용하여 데이터베이스 연결 #

#### <a name="needed-nugets"></a>필요한 누겟

* [설치 마이크로 소프트.Azure.Management.kusto](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/).
* [인증을 위해 Microsoft.Rest.ClientRuntime.Azure.인증을](https://www.nuget.org/packages/Microsoft.Rest.ClientRuntime.Azure.Authentication)설치합니다.

#### <a name="code-example"></a>코드 예

```Csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client secret
var leaderSubscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";
var followerSubscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";

var serviceCreds = await ApplicationTokenProvider.LoginSilentAsync(tenantId, clientId, clientSecret);
var resourceManagementClient = new KustoManagementClient(serviceCreds){
    SubscriptionId = followerSubscriptionId
};

var followerResourceGroupName = "followerResouceGroup";
var leaderResourceGroup = "leaderResouceGroup";
var leaderClusterName = "leader";
var followerClusterName = "follower";
var attachedDatabaseConfigurationName = "uniqueNameForAttachedDatabaseConfiguration";
var databaseName = "db"; // Can be specific database name or * for all databases
var defaultPrincipalsModificationKind = "Union"; 
var location = "North Central US";

AttachedDatabaseConfiguration attachedDatabaseConfigurationProperties = new AttachedDatabaseConfiguration()
{
    ClusterResourceId = $"/subscriptions/{leaderSubscriptionId}/resourceGroups/{leaderResourceGroup}/providers/Microsoft.Kusto/Clusters/{leaderClusterName}",
    DatabaseName = databaseName,
    DefaultPrincipalsModificationKind = defaultPrincipalsModificationKind,
    Location = location
};

var attachedDatabaseConfigurations = resourceManagementClient.AttachedDatabaseConfigurations.CreateOrUpdate(followerResourceGroupName, followerClusterName, attachedDatabaseConfigurationName, attachedDatabaseConfigurationProperties);
```

### <a name="attach-a-database-using-python"></a>파이썬을 사용하여 데이터베이스 첨부

#### <a name="needed-modules"></a>필요한 모듈

```
pip install azure-common
pip install azure-mgmt-kusto
```

#### <a name="code-example"></a>코드 예

```python
from azure.mgmt.kusto import KustoManagementClient
from azure.mgmt.kusto.models import AttachedDatabaseConfiguration
from azure.common.credentials import ServicePrincipalCredentials
import datetime

#Directory (tenant) ID
tenant_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Application ID
client_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Client Secret
client_secret = "xxxxxxxxxxxxxx"
follower_subscription_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
leader_subscription_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
credentials = ServicePrincipalCredentials(
        client_id=client_id,
        secret=client_secret,
        tenant=tenant_id
    )
kusto_management_client = KustoManagementClient(credentials, follower_subscription_id)

follower_resource_group_name = "followerResouceGroup"
leader_resouce_group_name = "leaderResouceGroup"
follower_cluster_name = "follower"
leader_cluster_name = "leader"
attached_database_Configuration_name = "uniqueNameForAttachedDatabaseConfiguration"
database_name  = "db" # Can be specific database name or * for all databases
default_principals_modification_kind  = "Union"
location = "North Central US"
cluster_resource_id = "/subscriptions/" + leader_subscription_id + "/resourceGroups/" + leader_resouce_group_name + "/providers/Microsoft.Kusto/Clusters/" + leader_cluster_name

attached_database_configuration_properties = AttachedDatabaseConfiguration(cluster_resource_id = cluster_resource_id, database_name = database_name, default_principals_modification_kind = default_principals_modification_kind, location = location)

#Returns an instance of LROPoller, see https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
poller = kusto_management_client.attached_database_configurations.create_or_update(follower_resource_group_name, follower_cluster_name, attached_database_Configuration_name, attached_database_configuration_properties)
```

### <a name="attach-a-database-using-an-azure-resource-manager-template"></a>Azure 리소스 관리자 템플릿을 사용하여 데이터베이스 연결

이 섹션에서는 [Azure 리소스 관리자 템플릿을](/azure/azure-resource-manager/management/overview)사용하여 기존 클런저에 데이터베이스를 연결하는 방법을 배웁니다. 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "followerClusterName": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "Name of the cluster to which the database will be attached."
            }
        },
        "attachedDatabaseConfigurationsName": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "Name of the attached database configurations to create."
            }
        },
        "databaseName": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The name of the database to follow. You can follow all databases by using '*'."
            }
        },
        "leaderClusterResourceId": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The resource ID of the leader cluster."
            }
        },
        "defaultPrincipalsModificationKind": {
            "type": "string",
            "defaultValue": "Union",
            "metadata": {
                "description": "The default principal modification kind."
            }
        },
        "location": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "Location for all resources."
            }
        }
    },
    "variables": {},
    "resources": [
        {
            "name": "[concat(parameters('followerClusterName'), '/', parameters('attachedDatabaseConfigurationsName'))]",
            "type": "Microsoft.Kusto/clusters/attachedDatabaseConfigurations",
            "apiVersion": "2019-09-07",
            "location": "[parameters('location')]",
            "properties": {
                "databaseName": "[parameters('databaseName')]",
                "clusterResourceId": "[parameters('leaderClusterResourceId')]",
                "defaultPrincipalsModificationKind": "[parameters('defaultPrincipalsModificationKind')]"
            }
        }
    ]
}
```

### <a name="deploy-the-template"></a>템플릿 배포 

[Azure 포털을 사용하거나 powershell을 사용하여 Azure](https://portal.azure.com) 리소스 관리자 템플릿을 배포할 수 있습니다.

   ![템플릿 배포](media/follower/template-deployment.png)


|**설정**  |**설명**  |
|---------|---------|
|팔로워 클러스터 이름     |  추종자 클러스터의 이름; 템플릿이 배포될 위치입니다.  |
|첨부된 데이터베이스 구성 이름    |    연결된 데이터베이스의 이름은 개체를 구성합니다. 이름은 클러스터 수준에서 고유한 문자열일 수 있습니다.     |
|데이터베이스 이름     |      따라야 할 데이터베이스의 이름입니다. 모든 리더의 데이터베이스를 따르려면 '*'를 사용합니다.   |
|리더 클러스터 리소스 ID    |   리더 클러스터의 리소스 ID입니다.      |
|기본 보안 주체 수정 종류    |   기본 주 수정 종류입니다. 할 `Union`수 `Replace` `None`있습니다 . 기본 주 수정 종류에 대한 자세한 내용은 [주 수정 종류 제어 명령을](kusto/management/cluster-follower.md#alter-follower-database-principals-modification-kind)참조하십시오.      |
|위치   |   모든 리소스의 위치입니다. 지시자와 추종자는 같은 위치에 있어야 합니다.       |
 
### <a name="verify-that-the-database-was-successfully-attached"></a>데이터베이스가 성공적으로 연결되었는지 확인

데이터베이스가 성공적으로 연결되었는지 확인하려면 [Azure 포털](https://portal.azure.com)에서 연결된 데이터베이스를 찾습니다. 

1. 팔로워 클러스터로 이동하여 **데이터베이스** 선택
1. 데이터베이스 목록에서 새 읽기 전용 데이터베이스를 검색합니다.

    ![읽기 전용 팔로워 데이터베이스](media/follower/read-only-follower-database.png)

또는 다음과 같습니다.

1. 리더 클러스터로 이동하여 **데이터베이스** 선택
2. 관련 데이터베이스가 > 다른 사용자와 **공유로**표시되어 있는지**확인합니다 예.**

    ![첨부된 데이터베이스 읽기 및 쓰기](media/follower/read-write-databases-shared.png)

## <a name="detach-the-follower-database-using-c"></a>C를 사용하여 추종자 데이터베이스를 분리 # 

### <a name="detach-the-attached-follower-database-from-the-follower-cluster"></a>연결된 팔로워 데이터베이스를 팔로워 클러스터에서 분리

추종자 클러스터는 다음과 같이 연결된 데이터베이스를 분리할 수 있습니다.

```csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client secret
var leaderSubscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";
var followerSubscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";

var serviceCreds = await ApplicationTokenProvider.LoginSilentAsync(tenantId, clientId, clientSecret);
var resourceManagementClient = new KustoManagementClient(serviceCreds){
    SubscriptionId = followerSubscriptionId
};

var followerResourceGroupName = "testrg";
//The cluster and database that are created as part of the prerequisites
var followerClusterName = "follower";
var attachedDatabaseConfigurationsName = "uniqueName";

resourceManagementClient.AttachedDatabaseConfigurations.Delete(followerResourceGroupName, followerClusterName, attachedDatabaseConfigurationsName);
```

### <a name="detach-the-attached-follower-database-from-the-leader-cluster"></a>연결된 팔로워 데이터베이스를 리더 클러스터에서 분리

리더 클러스터는 다음과 같이 연결된 데이터베이스를 분리할 수 있습니다.

```csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client secret
var leaderSubscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";
var followerSubscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";

var serviceCreds = await ApplicationTokenProvider.LoginSilentAsync(tenantId, clientId, clientSecret);
var resourceManagementClient = new KustoManagementClient(serviceCreds){
    SubscriptionId = leaderSubscriptionId
};

var leaderResourceGroupName = "testrg";
var followerResourceGroupName = "followerResouceGroup";
var leaderClusterName = "leader";
var followerClusterName = "follower";
//The cluster and database that are created as part of the Prerequisites
var followerDatabaseDefinition = new FollowerDatabaseDefinition()
    {
        AttachedDatabaseConfigurationName = "uniqueName",
        ClusterResourceId = $"/subscriptions/{followerSubscriptionId}/resourceGroups/{followerResourceGroupName}/providers/Microsoft.Kusto/Clusters/{followerClusterName}"
    };

resourceManagementClient.Clusters.DetachFollowerDatabases(leaderResourceGroupName, leaderClusterName, followerDatabaseDefinition);
```

## <a name="detach-the-follower-database-using-python"></a>파이썬을 사용하여 추종자 데이터베이스를 분리

### <a name="detach-the-attached-follower-database-from-the-follower-cluster"></a>연결된 팔로워 데이터베이스를 팔로워 클러스터에서 분리

추종자 클러스터는 다음과 같이 연결된 데이터베이스를 분리할 수 있습니다.

```python
from azure.mgmt.kusto import KustoManagementClient
from azure.common.credentials import ServicePrincipalCredentials
import datetime

#Directory (tenant) ID
tenant_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Application ID
client_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Client Secret
client_secret = "xxxxxxxxxxxxxx"
follower_subscription_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
credentials = ServicePrincipalCredentials(
        client_id=client_id,
        secret=client_secret,
        tenant=tenant_id
    )
kusto_management_client = KustoManagementClient(credentials, follower_subscription_id)

follower_resource_group_name = "followerResouceGroup"
follower_cluster_name = "follower"
attached_database_configurationName = "uniqueName"

#Returns an instance of LROPoller, see https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
poller = kusto_management_client.attached_database_configurations.delete(follower_resource_group_name, follower_cluster_name, attached_database_configurationName)
```

### <a name="detach-the-attached-follower-database-from-the-leader-cluster"></a>연결된 팔로워 데이터베이스를 리더 클러스터에서 분리

리더 클러스터는 다음과 같이 연결된 데이터베이스를 분리할 수 있습니다.

```python

from azure.mgmt.kusto import KustoManagementClient
from azure.mgmt.kusto.models import FollowerDatabaseDefinition
from azure.common.credentials import ServicePrincipalCredentials
import datetime

#Directory (tenant) ID
tenant_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Application ID
client_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Client Secret
client_secret = "xxxxxxxxxxxxxx"
follower_subscription_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
leader_subscription_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
credentials = ServicePrincipalCredentials(
        client_id=client_id,
        secret=client_secret,
        tenant=tenant_id
    )
kusto_management_client = KustoManagementClient(credentials, follower_subscription_id)

follower_resource_group_name = "followerResourceGroup"
leader_resource_group_name = "leaderResourceGroup"
follower_cluster_name = "follower"
leader_cluster_name = "leader"
attached_database_configuration_name = "uniqueName"
location = "North Central US"
cluster_resource_id = "/subscriptions/" + follower_subscription_id + "/resourceGroups/" + follower_resource_group_name + "/providers/Microsoft.Kusto/Clusters/" + follower_cluster_name


#Returns an instance of LROPoller, see https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
poller = kusto_management_client.clusters.detach_follower_databases(resource_group_name = leader_resource_group_name, cluster_name = leader_cluster_name, cluster_resource_id = cluster_resource_id, attached_database_configuration_name = attached_database_configuration_name)
```

## <a name="manage-principals-permissions-and-caching-policy"></a>보안 주체, 사용 권한 및 캐싱 정책 관리

### <a name="manage-principals"></a>보안 주체 관리

데이터베이스를 연결할 때 **"기본 보안 주체 수정 종류"를 지정합니다.** 기본값은 [권한 있는 보안 주체의](kusto/management/access-control/index.md#authorization) 리더 데이터베이스 컬렉션을 유지하는 것입니다.

|**종류** |**설명**  |
|---------|---------|
|**Union**     |   연결된 데이터베이스 보안 주체에는 항상 원래 데이터베이스 보안 주체와 추종자 데이터베이스에 추가된 추가 새 보안 주체가 포함됩니다.      |
|**교체**   |    원래 데이터베이스에서 보안 주체의 상속이 없습니다. 연결된 데이터베이스에 대해 새 보안 주체를 만들어야 합니다.     |
|**없음**   |   연결된 데이터베이스 보안 주체에는 추가 보안 주체가 없는 원래 데이터베이스의 보안 주체만 포함됩니다.      |

제어 명령을 사용하여 권한이 부여된 보안 주체를 구성하는 자세한 내용은 [추종자 클러스터 관리를 위한 제어 명령을](kusto/management/cluster-follower.md)참조하십시오.

### <a name="manage-permissions"></a>사용 권한 관리

읽기 전용 데이터베이스 사용 권한 관리는 모든 데이터베이스 형식과 동일합니다. [Azure 포털에서 권한 관리를](/azure/data-explorer/manage-database-permissions#manage-permissions-in-the-azure-portal)참조하십시오.

### <a name="configure-caching-policy"></a>캐싱 정책 구성

추종자 데이터베이스 관리자는 연결된 데이터베이스의 [캐싱 정책](kusto/management/cache-policy.md) 또는 호스팅 클러스터의 테이블을 수정할 수 있습니다. 기본값은 데이터베이스 및 테이블 수준 캐싱 정책의 리더 데이터베이스 컬렉션을 유지하는 것입니다. 예를 들어 월별 보고를 실행하기 위한 리더 데이터베이스에 30일 캐싱 정책과 팔로워 데이터베이스의 3일 캐싱 정책을 사용하여 최신 데이터만 쿼리하여 문제 해결을 수행할 수 있습니다. 컨트롤 명령을 사용하여 팔로워 데이터베이스 또는 테이블의 캐싱 정책을 구성하는 방법에 대한 자세한 내용은 [추종자 클러스터 관리를 위한 컨트롤 명령을](kusto/management/cluster-follower.md)참조하십시오.

## <a name="limitations"></a>제한 사항

* 추종자와 지시선 클러스터는 동일한 지역에 있어야 합니다.
* [스트리밍 빌드는](/azure/data-explorer/ingest-data-streaming) 팔로우중인 데이터베이스에서 사용할 수 없습니다.
* [고객 관리 키를](/azure/data-explorer/security#customer-managed-keys-with-azure-key-vault) 사용한 데이터 암호화는 리더 및 팔로워 클러스터 모두에서 지원되지 않습니다. 
* 다른 클러스터에 연결된 데이터베이스는 분리하기 전에 삭제할 수 없습니다.
* 데이터베이스를 분리하기 전에 다른 클러스터에 연결된 데이터베이스가 있는 클러스터는 삭제할 수 없습니다.
* 팔로워 또는 지시자 데이터베이스가 연결된 클러스터는 중지할 수 없습니다. 

## <a name="next-steps"></a>다음 단계

* 팔로워 클러스터 구성에 대한 자세한 내용은 [팔로워 클러스터 관리를 위한 제어 명령을](kusto/management/cluster-follower.md)참조하십시오.
