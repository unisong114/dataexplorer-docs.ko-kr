---
title: Azure 데이터 탐색기 클러스터에 대해 관리되는 ID를 구성하는 방법
description: Azure 데이터 탐색기 클러스터에 대해 관리되는 ID를 구성하는 방법을 알아봅니다.
author: saguiitay
ms.author: itsagui
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: 900bf815917a4b7c9841860d663a2183b1ab71b3
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81499463"
---
# <a name="configure-managed-identities-for-your-azure-data-explorer-cluster"></a>Azure 데이터 탐색기 클러스터에 대해 관리되는 ID 구성

[Azure Active Directory에서 관리되는 ID를](/azure/active-directory/managed-identities-azure-resources/overview) 사용하면 클러스터가 Azure 키 자격 증명 모음과 같은 다른 AAD 보호 리소스에 쉽게 액세스할 수 있습니다. ID는 Azure 플랫폼에서 관리되며 비밀을 프로비전하거나 회전할 필요가 없습니다. 이 문서에서는 Azure Data Explorer 클러스터에 대해 관리되는 ID를 만드는 방법을 보여 주며, 이 문서에서는 관리되는 ID 구성은 현재 [클러스터에 대한 고객 관리 키를 사용하도록 설정하기](/azure/data-explorer/security#customer-managed-keys-with-azure-key-vault)위해서만 지원됩니다.

> [!Note]
> Azure Data Explorer에 대한 관리되는 ID는 구독 또는 테넌트 간에 Azure Data Explorer 클러스터가 마이그레이션되는 경우 예상대로 작동하지 않습니다. 앱은 기능을 [비활성화하고](#disable-a-system-assigned-identity) [다시 활성화하여](#add-a-system-assigned-identity) 수행할 수 있는 새 ID를 얻어야 합니다. 새 ID를 사용하려면 다운스트림 리소스의 액세스 정책도 업데이트해야 합니다.

## <a name="add-a-system-assigned-identity"></a>시스템 할당된 ID 추가
                                                                                                    
클러스터에 연결된 시스템 할당 ID를 할당하고 클러스터가 삭제되면 삭제됩니다. 클러스터는 하나의 시스템 할당 ID만 가질 수 있습니다. 시스템 할당된 ID로 클러스터를 만들려면 클러스터에 추가 속성을 설정해야 합니다. 시스템 할당 된 ID는 아래에 설명 된 C #, ARM 템플릿 또는 Azure 포털을 사용 하 여 추가 됩니다.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

### <a name="add-a-system-assigned-identity-using-the-azure-portal"></a>Azure 포털을 사용하여 시스템 할당된 ID 추가

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

#### <a name="new-azure-data-explorer-cluster"></a>새 Azure 데이터 탐색기 클러스터

1. [Azure 데이터 탐색기 클러스터 만들기](/azure/data-explorer/create-cluster-database-portal#create-a-cluster) 
1. **id가** **할당된**보안 탭에서 > 를 **선택합니다.** 시스템에 할당된 ID를 제거하려면 **을 선택합니다.**
2. **다음:태그>** 또는 **검토 + 만들기를** 선택하여 클러스터를 만듭니다.

    ![새 클러스터에 시스템 할당된 ID 추가](media/managed-identities/system-assigned-identity-new-cluster.png)

#### <a name="existing-azure-data-explorer-cluster"></a>기존 Azure 데이터 탐색기 클러스터

1. 기존 Azure 데이터 탐색기 클러스터를 엽니다.
1. 포털의 왼쪽 창에서 **설정** > **ID를** 선택합니다.
1. **Id** 창 > **시스템 할당** 탭에서:
   1. **상태** 슬라이더를 **켜기로**이동합니다.
   1. **저장**을 선택합니다.
   1. 팝업 창에서 **예선택**

    ![시스템 할당된 ID 추가](media/managed-identities/turn-system-assigned-identity-on.png)

1. 몇 분 후 화면에 다음이 표시됩니다. 
  * **개체 ID** - 고객 관리 키에 사용 
  * 역할 할당 - 링크를 클릭하여 관련 역할을 **할당합니다.**

    ![시스템 할당된 ID](media/managed-identities/system-assigned-identity-on.png)

# <a name="c"></a>[C#](#tab/c-sharp)

### <a name="add-a-system-assigned-identity-using-c"></a>C를 사용하여 시스템 할당 ID 추가 #

#### <a name="prerequisites"></a>사전 요구 사항

Azure 데이터 탐색기 C# 클라이언트를 사용하여 관리되는 ID를 설정하려면 다음을 수행하십시오.

* Azure [데이터 탐색기(Kusto) NuGet 패키지를](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/)설치합니다.
* 인증을 위해 [Microsoft.IdentityModel.Client.ActiveDirectory NuGet 패키지를](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) 설치합니다.
* 리소스에 액세스할 수 있는 [Azure AD 응용 프로그램](/azure/active-directory/develop/howto-create-service-principal-portal) 및 서비스 주체를 만듭니다. 구독 범위에 역할 할당을 추가하고 필수 `Directory (tenant) ID` `Application ID`을 `Client Secret`가져옵니다.

#### <a name="create-or-update-your-cluster"></a>클러스터 만들기 또는 업데이트

1. 속성을 사용하여 클러스터를 `Identity` 만들거나 업데이트합니다.

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
    
2. 다음 명령을 실행하여 클러스터가 ID로 성공적으로 생성또는 업데이트되었는지 확인합니다.

    ```csharp
    kustoManagementClient.Clusters.Get(resourceGroupName, clusterName);
    ```

    결과가 `Succeeded` 값과 `ProvisioningState` 함께 포함된 경우 클러스터가 만들어지거나 업데이트되었으며 다음 속성이 있어야 합니다.

    ```csharp
    var principalId = cluster.Identity.PrincipalId;
    var tenantId = cluster.Identity.TenantId;
    ```

`PrincipalId`GUID로 `TenantId` 대체됩니다. 속성은 `TenantId` ID가 속한 AAD 테넌트를 식별합니다. 는 `PrincipalId` 클러스터의 새 ID에 대한 고유 식별자입니다. AAD 내에서 서비스 주체는 사용자가 App Service 또는 Azure Functions 인스턴스에 지정한 이름과 동일한 이름을 갖습니다.

# <a name="arm-template"></a>[ARM 템플릿](#tab/arm)

### <a name="add-a-system-assigned-identity-using-an-azure-resource-manager-template"></a>Azure 리소스 관리자 템플릿을 사용하여 시스템 할당된 ID 추가

Azure Resource Manager 템플릿을 사용하여 Azure 리소스 배포를 자동화할 수 있습니다. Azure 데이터 탐색기 배포에 대한 자세한 내용은 [Azure 리소스 관리자 템플릿을 사용하여 Azure 데이터 탐색기 클러스터 및 데이터베이스 만들기를](create-cluster-database-resource-manager.md)참조하십시오.

시스템 할당 형식을 추가하면 Azure가 클러스터의 ID를 만들고 관리하도록 지시합니다. 모든 `Microsoft.Kusto/clusters` 유형의 리소스는 ID를 사용하여 리소스 정의에 다음 속성을 포함하는 방법으로 만들 수 있습니다. 

```json
"identity": {
    "type": "SystemAssigned"
}    
```

다음은 그 예입니다.

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

클러스터를 만들 때 다음과 같은 추가 속성이 있습니다.

```json
"identity": {
    "type": "SystemAssigned",
    "tenantId": "<TENANTID>",
    "principalId": "<PRINCIPALID>"
}
```

`<TENANTID>`GUID로 `<PRINCIPALID>` 대체됩니다. 속성은 `TenantId` ID가 속한 AAD 테넌트를 식별합니다. 는 `PrincipalId` 클러스터의 새 ID에 대한 고유 식별자입니다. AAD 내에서 서비스 주체는 사용자가 App Service 또는 Azure Functions 인스턴스에 지정한 이름과 동일한 이름을 갖습니다.

---

## <a name="disable-a-system-assigned-identity"></a>시스템 할당 된 ID를 사용 하지 않도록 설정

시스템 할당 된 ID를 제거 하면 AAD에서 삭제 됩니다. 클러스터 리소스가 삭제되면 AAD에서 시스템 할당ID도 자동으로 제거됩니다. 기능을 사용하지 않도록 설정하여 시스템 할당 ID를 제거할 수 있습니다.  시스템 할당 된 ID는 아래에 설명 된 대로 C #, ARM 템플릿 또는 Azure 포털을 사용 하 여 제거 됩니다.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

### <a name="disable-a-system-assigned-identity-using-the-azure-portal"></a>Azure 포털을 사용하여 시스템 할당 된 ID를 사용 하지 않도록 설정 합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. 포털의 왼쪽 창에서 **설정** > **ID를** 선택합니다.
1. **Id** 창 > **시스템 할당** 탭에서:
    1. **상태** 슬라이더를 **끄기로**이동합니다.
    1. **저장**을 선택합니다.
    1. 팝업 창에서 **예를** 선택하여 시스템 할당된 ID를 비활성화합니다. **ID** 창은 시스템에서 할당된 ID를 추가하기 전과 동일한 상태로 되돌아갑니다.

    ![시스템 할당된 ID 끄기](media/managed-identities/system-assigned-identity.png)

# <a name="c"></a>[C#](#tab/c-sharp)

### <a name="remove-a-system-assigned-identity-using-c"></a>C를 사용하여 시스템 할당 된 ID 제거 #

다음을 실행하여 시스템 할당된 ID를 제거합니다.

```csharp
var identity = new Identity(IdentityType.None);
var cluster = new Cluster(location, sku, identity: identity);
await kustoManagementClient.Clusters.CreateOrUpdateAsync(resourceGroupName, clusterName, cluster);
```

# <a name="arm-template"></a>[ARM 템플릿](#tab/arm)

### <a name="remove-a-system-assigned-identity-using-an-azure-resource-manager-template"></a>Azure 리소스 관리자 템플릿을 사용하여 시스템 할당된 ID 제거

다음을 실행하여 시스템 할당된 ID를 제거합니다.

```json
"identity": {
    "type": "None"
}
```

---

## <a name="next-steps"></a>다음 단계

* [Azure에서 Azure 데이터 탐색기 클러스터 보안](security.md)
* Azure 데이터 탐색기 - 미사용 암호화를 사용하도록 설정하여 [Azure 포털에서 클러스터를 보호합니다.](manage-cluster-security.md)
 * [C를 사용하여 고객 관리 키 구성 #](customer-managed-keys-csharp.md)
 * [Azure 리소스 관리자 템플릿을 사용하여 고객 관리 키 구성](customer-managed-keys-resource-manager.md)
