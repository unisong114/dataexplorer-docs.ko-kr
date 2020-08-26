---
title: Azure 데이터 탐색기에서 클러스터를 만드는 동안 인프라 암호화 (이중 암호화)를 사용 하도록 설정
description: 이 문서에서는 Azure 데이터 탐색기에서 클러스터를 만드는 동안 인프라 암호화 (이중 암호화)를 사용 하도록 설정 하는 방법을 설명 합니다.
author: orspod
ms.author: orspodek
ms.reviewer: toleibov
ms.service: data-explorer
ms.topic: how-to
ms.date: 08/11/2020
ms.openlocfilehash: e89ce6f77545b4f0b42cbb3d792edd5ceeb0ed34
ms.sourcegitcommit: f354accde64317b731f21e558c52427ba1dd4830
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88874677"
---
# <a name="enable-infrastructure-encryption-double-encryption-during-cluster-creation-in-azure-data-explorer"></a>Azure 데이터 탐색기에서 클러스터를 만드는 동안 인프라 암호화 (이중 암호화)를 사용 하도록 설정
  
클러스터를 만들 때 해당 저장소는 [서비스 수준에서 자동으로 암호화](/azure/storage/common/storage-service-encryption)됩니다. 데이터 보안을 강화 해야 하는 경우에는 이중 암호화 라고도 하는 [Azure Storage 인프라 수준 암호화](/azure/storage/common/infrastructure-encryption-enable)를 사용 하도록 설정할 수도 있습니다. 인프라 암호화를 사용 하는 경우 저장소 계정의 데이터는 두 개의 서로 다른 암호화 알고리즘과 두 개의 다른 키를 사용 하 여 서비스 수준에서 한 번, 인프라 수준에서 한 번, 두 번 암호화 됩니다. Azure Storage 데이터의 이중 암호화는 암호화 알고리즘 또는 키 중 하나가 손상 될 수 있는 시나리오를 방지 합니다. 이 시나리오에서 추가 암호화 계층은 계속 해 서 데이터를 보호 합니다.

> [!IMPORTANT]
> * 이중 암호화를 사용 하도록 설정 하는 작업은 클러스터를 만드는 동안에만 가능 합니다.
> * 클러스터에서 인프라 암호화를 사용 하도록 설정한 후에는 사용 하지 않도록 설정할 **수 없습니다** .
> * 이중 암호화는 인프라 암호화가 지원 되는 지역 에서만 사용할 수 있습니다. 자세한 내용은 [저장소 인프라 암호화](/azure/storage/common/infrastructure-encryption-enable)를 참조 하세요.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

1. [Azure 데이터 탐색기 클러스터 만들기](create-cluster-database-portal.md#create-a-cluster) 
1. **보안** 탭에서 **이중 암호화 사용 > 설정** **을 선택 합니다**. 이중 암호화를 제거 하려면 **끄기**를 선택 합니다.
1. **다음: 네트워크>** 또는 **검토 + 만들기** 를 선택 하 여 클러스터를 만듭니다.

    :::image type="content" source="media/double-encryption/double-encryption-portal.png" alt-text="이중 암호화 새 클러스터":::


# <a name="c"></a>[C#](#tab/c-sharp)

C #을 사용 하 여 클러스터를 만드는 동안 인프라 암호화를 사용 하도록 설정할 수 있습니다.

## <a name="prerequisites"></a>전제 조건

Azure 데이터 탐색기 c # 클라이언트를 사용 하 여 관리 id를 설정 합니다.

* [Azure 데이터 탐색기 (Kusto) NuGet 패키지](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/)를 설치 합니다.
* 인증을 위해 [system.identitymodel. ActiveDirectory NuGet 패키지](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) 를 설치 합니다.
* 리소스에 액세스할 수 있는 [AZURE AD 응용 프로그램](/azure/active-directory/develop/howto-create-service-principal-portal) 및 서비스 주체를 만듭니다. 구독 범위에서 역할 할당을 추가 하 고 필수 `Directory (tenant) ID` , 및를 가져옵니다 `Application ID` `Client Secret` .

## <a name="create-your-cluster"></a>클러스터 만들기

1. 다음 속성을 사용 하 여 클러스터를 만듭니다 `enableDoubleEncryption` .

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
    var location = "East US";
    var skuName = "Standard_D13_v2";
    var tier = "Standard";
    var capacity = 5;
    var sku = new AzureSku(skuName, tier, capacity);
    var enableDoubleEncryption = true;
    var cluster = new Cluster(location, sku, enableDoubleEncryption: enableDoubleEncryption);
    await kustoManagementClient.Clusters.CreateOrUpdateAsync(resourceGroupName, clusterName, cluster);
    ```
    
1. 다음 명령을 실행 하 여 클러스터가 성공적으로 만들어졌는지 확인 합니다.

    ```csharp
    kustoManagementClient.Clusters.Get(resourceGroupName, clusterName);
    ```

    결과에 값이 포함 되어 있으면 `ProvisioningState` `Succeeded` 클러스터가 성공적으로 생성 된 것입니다.

# <a name="arm-template"></a>[ARM 템플릿](#tab/arm)

Azure Resource Manager를 사용 하 여 클러스터를 만드는 동안 인프라 암호화를 사용 하도록 설정할 수 있습니다.

Azure Resource Manager 템플릿을 사용하여 Azure 리소스 배포를 자동화할 수 있습니다. Azure 데이터 탐색기에 배포 하는 방법에 대해 자세히 알아보려면 [Azure Resource Manager 템플릿을 사용 하 여 azure 데이터 탐색기 클러스터 및 데이터베이스 만들기](create-cluster-database-resource-manager.md)를 참조 하세요.

## <a name="add-a-system-assigned-identity-using-an-azure-resource-manager-template"></a>Azure Resource Manager 템플릿을 사용 하 여 시스템 할당 id 추가

1. ' EnableDoubleEncryption ' 형식을 추가 하 여 Azure에 클러스터에 대 한 인프라 암호화 (이중 암호화)를 사용 하도록 지시 합니다.
    
    ```json
    {
        "apiVersion": "2020-06-14",
        "type": "Microsoft.Kusto/clusters",
        "name": "[variables('clusterName')]",
        "location": "[resourceGroup().location]",
        "properties": {
            "trustedExternalTenants": [],
            "virtualNetworkConfiguration": null,
            "optimizedAutoscale": null,
            "enableDiskEncryption": false,
            "enableStreamingIngest": false,
            "enableDoubleEncryption": true,
        }
    }
    ```

1. 클러스터를 만들면 다음과 같은 추가 속성이 있습니다.

    ```json
    "identity": {
        "type": "SystemAssigned",
        "tenantId": "<TENANTID>",
        "principalId": "<PRINCIPALID>"
    }
    ```
---

## <a name="next-steps"></a>다음 단계

[클러스터 상태 확인](check-cluster-health.md)
