---
title: 'C를 사용 하 여 Azure 데이터 탐색기에 대 한 클러스터 보안 주체 추가 #'
description: '이 문서에서는 c #을 사용 하 여 Azure 데이터 탐색기에 대 한 클러스터 보안 주체를 추가 하는 방법에 대해 알아봅니다.'
author: orspod
ms.author: orspodek
ms.reviewer: lugoldbe
ms.service: data-explorer
ms.topic: how-to
ms.date: 02/03/2020
ms.openlocfilehash: a7079235d3ab43bd193b7e4c0f7cbe25689a5d3e
ms.sourcegitcommit: f354accde64317b731f21e558c52427ba1dd4830
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88872304"
---
# <a name="add-cluster-principals-for-azure-data-explorer-by-using-c"></a>C를 사용 하 여 Azure 데이터 탐색기에 대 한 클러스터 보안 주체 추가 #

> [!div class="op_single_selector"]
> * [C#](cluster-principal-csharp.md)
> * [Python](cluster-principal-python.md)
> * [Azure Resource Manager 템플릿](cluster-principal-resource-manager.md)

Azure 데이터 탐색기는 로그 및 원격 분석 데이터에 사용 가능한 빠르고 확장성이 우수한 데이터 탐색 서비스입니다. 이 문서에서는 c #을 사용 하 여 Azure 데이터 탐색기에 대 한 클러스터 보안 주체를 추가 합니다.

## <a name="prerequisites"></a>전제 조건

* Visual Studio 2019가 설치되지 않은 경우 **체험판** [Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/)을 다운로드하고 사용할 수 있습니다. Visual Studio를 설치하는 동안 **Azure 개발**을 사용하도록 설정합니다.
* Azure 구독이 아직 없는 경우 시작하기 전에 [Azure 체험 계정](https://azure.microsoft.com/free/)을 만듭니다.
* [클러스터를 만듭니다](create-cluster-database-csharp.md).

## <a name="install-c-nuget"></a>C # NuGet 설치

* [Microsoft. ..kusto를](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/)설치 합니다.
* 인증을 위해 [Microsoft Azure 인증](https://www.nuget.org/packages/Microsoft.Rest.ClientRuntime.Azure.Authentication) 을 설치 합니다.

[!INCLUDE [data-explorer-authentication](includes/data-explorer-authentication.md)]

## <a name="add-a-cluster-principal"></a>클러스터 보안 주체 추가

다음 예제에서는 프로그래밍 방식으로 클러스터 주체를 추가 하는 방법을 보여 줍니다.

```csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client Secret
var subscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";

var serviceCreds = await ApplicationTokenProvider.LoginSilentAsync(tenantId, clientId, clientSecret);
var kustoManagementClient = new KustoManagementClient(serviceCreds)
{
    SubscriptionId = subscriptionId
};

var resourceGroupName = "testrg";
//The cluster that is created as part of the Prerequisites
var clusterName = "mykustocluster";
string principalAssignmentName = "clusterPrincipalAssignment1";
string principalId = "xxxxxxxx";//User email, application ID, or security group name
string role = "AllDatabasesAdmin";//AllDatabasesAdmin or AllDatabasesViewer
string tenantIdForPrincipal = tenantId;
string principalType = "App";//User, App, or Group

var clusterPrincipalAssignment = new ClusterPrincipalAssignment(principalId, role, principalType, tenantId: tenantIdForPrincipal);
await kustoManagementClient.ClusterPrincipalAssignments.CreateOrUpdateAsync(resourceGroupName, clusterName, principalAssignmentName, clusterPrincipalAssignment);
```

|**설정** | **제안 값** | **필드 설명**|
|---|---|---|
| tenantId | *xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx-xxxxx-xxxxxxxxx* | 테넌트 ID 디렉터리 ID 라고도 합니다.|
| subscriptionId | *xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx-xxxxx-xxxxxxxxx* | 리소스를 만드는 데 사용 하는 구독 ID입니다.|
| clientId | *xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx-xxxxx-xxxxxxxxx* | 테 넌 트의 리소스에 액세스할 수 있는 응용 프로그램의 클라이언트 ID입니다.|
| clientSecret | *xxxxxxxxxxxxxx* | 테 넌 트의 리소스에 액세스할 수 있는 응용 프로그램의 클라이언트 암호입니다. |
| resourceGroupName | *testrg* | 클러스터를 포함 하는 리소스 그룹의 이름입니다.|
| clusterName | *mykustocluster* | 클러스터의 이름입니다.|
| principalAssignmentName | *clusterPrincipalAssignment1* | 클러스터 보안 주체 리소스의 이름입니다.|
| principalId | *xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx-xxxxx-xxxxxxxxx* | 사용자 전자 메일, 응용 프로그램 ID 또는 보안 그룹 이름일 수 있는 보안 주체 ID입니다.|
| 역할(role) | *AllDatabasesAdmin* | ' AllDatabasesAdmin' ' 또는 ' AllDatabasesViewer ' 일 수 있는 클러스터 보안 주체의 역할입니다.|
| tenantIdForPrincipal | *xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx-xxxxx-xxxxxxxxx* | 보안 주체의 테 넌 트 ID입니다.|
| principalType | *App* | ' 사용자 ', ' 앱 ' 또는 ' 그룹 ' 일 수 있는 보안 주체의 유형입니다.|

## <a name="next-steps"></a>다음 단계

* [데이터베이스 보안 주체 추가](database-principal-csharp.md)
