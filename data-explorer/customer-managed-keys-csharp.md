---
title: 'C를 사용하여 고객 관리 키 구성 #'
description: 이 문서에서는 Azure Data Explorer의 데이터에 대해 고객 관리 키 암호화를 구성하는 방법을 설명합니다.
author: saguiitay
ms.author: itsagui
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: e394d928774624ac3c7faacab7726570272da82a
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81497487"
---
# <a name="configure-customer-managed-keys-using-c"></a>C를 사용하여 고객 관리 키 구성 #

> [!div class="op_single_selector"]
> * [포털](customer-managed-keys-portal.md)
> * [C#](customer-managed-keys-csharp.md)
> * [Azure Resource Manager 템플릿](customer-managed-keys-resource-manager.md)

[!INCLUDE [data-explorer-configure-customer-managed-keys](includes/data-explorer-configure-customer-managed-keys.md)]

[!INCLUDE [data-explorer-configure-customer-managed-keys part 2](includes/data-explorer-configure-customer-managed-keys-b.md)]

## <a name="configure-encryption-with-customer-managed-keys"></a>고객 관리 키로 암호화 구성

이 섹션에서는 Azure Data Explorer C# 클라이언트를 사용하여 고객 관리 키 암호화를 구성하는 방법을 보여 주며 있습니다. 

### <a name="prerequisites"></a>사전 요구 사항

* Visual Studio 2019가 설치되지 않은 경우 **체험판** [Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/)을 다운로드하고 사용할 수 있습니다. Visual Studio 설정 중에 **Azure 개발을** 사용하도록 설정해야 합니다.

* Azure 구독이 아직 없는 경우 시작하기 전에 [Azure 체험 계정](https://azure.microsoft.com/free/)을 만듭니다.

### <a name="install-c-nuget"></a>C# NuGet 설치

* Azure [데이터 탐색기(Kusto) NuGet 패키지를](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/)설치합니다.

* 인증을 위해 [Microsoft.IdentityModel.Client.ActiveDirectory NuGet 패키지를](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) 설치합니다.

### <a name="authentication"></a>인증

이 문서의 예제를 실행하려면 리소스에 액세스할 수 있는 [Azure AD 응용 프로그램](/azure/active-directory/develop/howto-create-service-principal-portal) 및 서비스 주체를 만듭니다. 구독 범위에서 역할 할당을 추가하고 필수 `Directory (tenant) ID`을 `Application ID`얻을 `Client Secret`수 있습니다.

### <a name="configure-cluster"></a>클러스터 구성

기본적으로 Azure 데이터 탐색기 암호화는 Microsoft에서 관리하는 키를 사용합니다. Azure Data Explorer 클러스터를 구성하여 고객 관리 키를 사용하고 클러스터와 연결할 키를 지정합니다.

1. 다음 코드를 사용하여 클러스터를 업데이트합니다.

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
    var keyName = "myKey";
    var keyVersion = "5b52b20e8d8a42e6bd7527211ae32654";
    var keyVaultUri = "https://mykeyvault.vault.azure.net/";
    var keyVaultProperties = new KeyVaultProperties (keyName, keyVersion, keyVaultUri);
    var clusterUpdate = new ClusterUpdate(keyVaultProperties: keyVaultProperties);
    await kustoManagementClient.Clusters.UpdateAsync(resourceGroupName, clusterName, clusterUpdate);
    ```

1. 다음 명령을 실행하여 클러스터가 성공적으로 업데이트되었는지 확인합니다.

    ```csharp
    kustoManagementClient.Clusters.Get(resourceGroupName, clusterName);
    ```

    결과에 `Succeeded` 값이 `ProvisioningState` 포함된 경우 클러스터가 성공적으로 업데이트되었습니다.

## <a name="update-the-key-version"></a>키 버전 업데이트

키의 새 버전을 만들 때 새 버전을 사용 하려면 클러스터를 업데이트 해야 합니다. 먼저 최신 `Get-AzKeyVaultKey` 버전의 키를 얻으려면 호출합니다. 그런 다음 클러스터의 키 자격 증명 모음 속성을 업데이트하여 [클러스터 구성에](#configure-cluster)표시된 대로 키의 새 버전을 사용합니다.

## <a name="next-steps"></a>다음 단계

* [Azure에서 Azure 데이터 탐색기 클러스터 보안](security.md)
* [Azure 데이터 탐색기 클러스터에 대해 관리되는 ID 구성](managed-identities.md)
* Azure 데이터 탐색기 - 미사용 암호화를 사용하도록 설정하여 [Azure 포털에서 클러스터를 보호합니다.](manage-cluster-security.md)
* [Azure 리소스 관리자 템플릿을 사용하여 고객 관리 키 구성](customer-managed-keys-resource-manager.md)


