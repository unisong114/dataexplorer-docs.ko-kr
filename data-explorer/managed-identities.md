---
title: Azure 데이터 탐색기 클러스터에 대 한 관리 되는 id를 구성 하는 방법
description: Azure 데이터 탐색기 클러스터에 대 한 관리 id를 구성 하는 방법을 알아봅니다.
author: orspod
ms.author: orspodek
ms.reviewer: itsagui
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: 59392c8ff555df99ccb7c4a5b7503266cb095552
ms.sourcegitcommit: d9fbcd6c9787f90de62e8e832c92d43b8090cbfc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/03/2020
ms.locfileid: "87515841"
---
# <a name="configure-managed-identities-for-your-azure-data-explorer-cluster"></a>Azure 데이터 탐색기 클러스터에 대 한 관리 id 구성

[Azure Active Directory에서 관리 되는 id](/azure/active-directory/managed-identities-azure-resources/overview) 를 사용 하면 클러스터에서 AZURE KEY VAULT 같은 AAD로 보호 되는 다른 리소스에 쉽게 액세스할 수 있습니다. Id는 Azure 플랫폼에서 관리 하며, 암호를 프로 비전 하거나 회전할 필요가 없습니다. 이 문서에서는 Azure 데이터 탐색기 클러스터에 대 한 관리 id를 만드는 방법을 보여 줍니다. 관리 id 구성은 현재 [클러스터에 대해 고객이 관리](security.md#customer-managed-keys-with-azure-key-vault)하는 키를 사용 하도록 설정 하는 경우에만 지원 됩니다.

> [!Note]
> Azure 데이터 탐색기에 대 한 관리 id는 구독 또는 테 넌 트 간에 마이그레이션된 Azure 데이터 탐색기 클러스터의 경우 예상 대로 작동 하지 않습니다. 앱은 기능을 [사용 하지 않도록](#disable-a-system-assigned-identity) 설정 하 고 [다시 사용 하도록 설정](#add-a-system-assigned-identity) 하 여 수행할 수 있는 새 id를 가져와야 합니다. 또한 새 id를 사용 하도록 다운스트림 리소스의 액세스 정책을 업데이트 해야 합니다.

## <a name="add-a-system-assigned-identity"></a>시스템 할당 ID 추가
                                                                                                    
클러스터에 연결 된 시스템 할당 id를 할당 하 고, 클러스터를 삭제 하면 삭제 됩니다. 클러스터에는 시스템 할당 id가 하나만 있을 수 있습니다. 시스템에 할당 된 id를 사용 하 여 클러스터를 만들려면 클러스터에서 추가 속성을 설정 해야 합니다. 시스템 할당 id는 c #, ARM 템플릿 또는 아래에 설명 된 Azure Portal를 사용 하 여 추가 됩니다.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

### <a name="add-a-system-assigned-identity-using-the-azure-portal"></a>Azure Portal를 사용 하 여 시스템 할당 id 추가

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

#### <a name="new-azure-data-explorer-cluster"></a>새 Azure 데이터 탐색기 클러스터

1. [Azure 데이터 탐색기 클러스터 만들기](create-cluster-database-portal.md#create-a-cluster) 
1. **시스템 할당 id**> **보안** 탭에서 **켜기**를 선택 합니다. 시스템 할당 id를 제거 하려면 **끄기**를 선택 합니다.
2. **다음: 태그>** 또는 **검토 + 만들기** 를 선택 하 여 클러스터를 만듭니다.

    ![새 클러스터에 시스템 할당 id 추가](media/managed-identities/system-assigned-identity-new-cluster.png)

#### <a name="existing-azure-data-explorer-cluster"></a>기존 Azure 데이터 탐색기 클러스터

1. 기존 Azure 데이터 탐색기 클러스터를 엽니다.
1. **Settings**  >  포털의 왼쪽 창에서 설정**id** 를 선택 합니다.
1. **Id** 창 > **시스템 할당 됨** 탭에서 다음을 수행 합니다.
   1. **상태** 슬라이더를 **켜기**로 이동 합니다.
   1. **저장**을 선택합니다.
   1. 팝업 창에서 **예** 를 선택 합니다.

    ![시스템 할당 id 추가](media/managed-identities/turn-system-assigned-identity-on.png)

1. 몇 분 후 화면에 다음이 표시 됩니다. 
  * **개체 ID** -고객 관리 키에 사용 됩니다. 
  * **역할 할당** -링크를 클릭 하 여 관련 역할을 할당 합니다.

    ![시스템 할당 id](media/managed-identities/system-assigned-identity-on.png)

# <a name="c"></a>[C#](#tab/c-sharp)

### <a name="add-a-system-assigned-identity-using-c"></a>C를 사용 하 여 시스템 할당 id 추가 #

#### <a name="prerequisites"></a>사전 요구 사항

Azure 데이터 탐색기 c # 클라이언트를 사용 하 여 관리 id를 설정 하려면:

* [Azure 데이터 탐색기 (Kusto) NuGet 패키지](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/)를 설치 합니다.
* 인증을 위해 [system.identitymodel. ActiveDirectory NuGet 패키지](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) 를 설치 합니다.
* 리소스에 액세스할 수 있는 [AZURE AD 응용 프로그램](/azure/active-directory/develop/howto-create-service-principal-portal) 및 서비스 주체를 만듭니다. 구독 범위에서 역할 할당을 추가 하 고 필수 `Directory (tenant) ID` , 및를 가져옵니다 `Application ID` `Client Secret` .

#### <a name="create-or-update-your-cluster"></a>클러스터 만들기 또는 업데이트

1. 속성을 사용 하 여 클러스터를 만들거나 업데이트 합니다 `Identity` .

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
    var identity = new Identity(IdentityType.SystemAssigned);
    var cluster = new Cluster(location, sku, identity: identity);
    await kustoManagementClient.Clusters.CreateOrUpdateAsync(resourceGroupName, clusterName, cluster);
    ```
    
2. 다음 명령을 실행 하 여 클러스터가 성공적으로 만들어지거나 id로 업데이트 되었는지 확인 합니다.

    ```csharp
    kustoManagementClient.Clusters.Get(resourceGroupName, clusterName);
    ```

    결과에 값이 포함 되어 있으면 `ProvisioningState` `Succeeded` 클러스터가 만들어지거나 업데이트 되며 다음과 같은 속성이 있어야 합니다.

    ```csharp
    var principalId = cluster.Identity.PrincipalId;
    var tenantId = cluster.Identity.TenantId;
    ```

`PrincipalId`및 `TenantId` 는 guid로 대체 됩니다. `TenantId`속성은 id가 속하는 AAD 테 넌 트를 식별 합니다. 는 `PrincipalId` 클러스터의 새 id에 대 한 고유 식별자입니다. AAD 내에서 서비스 주체는 사용자가 App Service 또는 Azure Functions 인스턴스에 지정한 이름과 동일한 이름을 갖습니다.

# <a name="arm-template"></a>[ARM 템플릿](#tab/arm)

### <a name="add-a-system-assigned-identity-using-an-azure-resource-manager-template"></a>Azure Resource Manager 템플릿을 사용 하 여 시스템 할당 id 추가

Azure Resource Manager 템플릿을 사용하여 Azure 리소스 배포를 자동화할 수 있습니다. Azure 데이터 탐색기에 배포 하는 방법에 대해 자세히 알아보려면 [Azure Resource Manager 템플릿을 사용 하 여 azure 데이터 탐색기 클러스터 및 데이터베이스 만들기](create-cluster-database-resource-manager.md)를 참조 하세요.

시스템 할당 유형을 추가 하면 Azure에서 클러스터에 대 한 id를 만들고 관리 하 게 됩니다. 모든 `Microsoft.Kusto/clusters` 유형의 리소스는 ID를 사용하여 리소스 정의에 다음 속성을 포함하는 방법으로 만들 수 있습니다. 

```json
"identity": {
    "type": "SystemAssigned"
}    
```

예를 들면 다음과 같습니다.

```json
{
    "apiVersion": "2019-09-07",
    "type": "Microsoft.Kusto/clusters",
    "name": "[variables('clusterName')]",
    "location": "[resourceGroup().location]",
    "identity": {
        "type": "SystemAssigned"
    },
    "properties": {
        "trustedExternalTenants": [],
        "virtualNetworkConfiguration": null,
        "optimizedAutoscale": null,
        "enableDiskEncryption": false,
        "enableStreamingIngest": false,
    }
}
```

클러스터를 만들면 다음과 같은 추가 속성이 있습니다.

```json
"identity": {
    "type": "SystemAssigned",
    "tenantId": "<TENANTID>",
    "principalId": "<PRINCIPALID>"
}
```

`<TENANTID>`및 `<PRINCIPALID>` 는 guid로 대체 됩니다. `TenantId`속성은 id가 속하는 AAD 테 넌 트를 식별 합니다. 는 `PrincipalId` 클러스터의 새 id에 대 한 고유 식별자입니다. AAD 내에서 서비스 주체는 사용자가 App Service 또는 Azure Functions 인스턴스에 지정한 이름과 동일한 이름을 갖습니다.

---

## <a name="disable-a-system-assigned-identity"></a>시스템 할당 id 사용 안 함

시스템 할당 id를 제거 하면 AAD 에서도 삭제 됩니다. 클러스터 리소스를 삭제 하면 시스템 할당 id도 AAD에서 자동으로 제거 됩니다. 시스템 할당 id는 기능을 사용 하지 않도록 설정 하 여 제거할 수 있습니다.  시스템 할당 id는 c #, ARM 템플릿 또는 아래에 설명 된 Azure Portal를 사용 하 여 제거 됩니다.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

### <a name="disable-a-system-assigned-identity-using-the-azure-portal"></a>Azure Portal를 사용 하 여 시스템 할당 id를 사용 하지 않도록 설정

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. **Settings**  >  포털의 왼쪽 창에서 설정**id** 를 선택 합니다.
1. **Id** 창 > **시스템 할당 됨** 탭에서 다음을 수행 합니다.
    1. **상태** 슬라이더를 **끄기**로 이동 합니다.
    1. **저장**을 선택합니다.
    1. 팝업 창에서 **예** 를 선택 하 여 시스템 할당 id를 사용 하지 않도록 설정 합니다. **Id** 창은 시스템 할당 id가 추가 되기 전에와 동일한 조건으로 되돌아갑니다.

    ![시스템 할당 id 꺼짐](media/managed-identities/system-assigned-identity.png)

# <a name="c"></a>[C#](#tab/c-sharp)

### <a name="remove-a-system-assigned-identity-using-c"></a>C를 사용 하 여 시스템 할당 id 제거 #

다음을 실행 하 여 시스템 할당 id를 제거 합니다.

```csharp
var identity = new Identity(IdentityType.None);
var cluster = new Cluster(location, sku, identity: identity);
await kustoManagementClient.Clusters.CreateOrUpdateAsync(resourceGroupName, clusterName, cluster);
```

# <a name="arm-template"></a>[ARM 템플릿](#tab/arm)

### <a name="remove-a-system-assigned-identity-using-an-azure-resource-manager-template"></a>Azure Resource Manager 템플릿을 사용 하 여 시스템 할당 id 제거

다음을 실행 하 여 시스템 할당 id를 제거 합니다.

```json
"identity": {
    "type": "None"
}
```

---

## <a name="next-steps"></a>다음 단계

* [Azure에서 Azure 데이터 탐색기 클러스터 보호](security.md)
* 미사용 암호화를 사용 하도록 설정 하 여 [Azure Azure Portal 데이터 탐색기에서 디스크 암호화를 사용 하 여 클러스터를 보호](cluster-disk-encryption.md) 합니다.
 * [C를 사용 하 여 고객 관리 키 구성 #](customer-managed-keys-csharp.md)
 * [Azure Resource Manager 템플릿을 사용 하 여 고객 관리 키 구성](customer-managed-keys-resource-manager.md)
