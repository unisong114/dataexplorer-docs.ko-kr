---
title: Azure 리소스 관리자 템플릿을 사용하여 Azure 데이터 탐색기 클러스터 및 데이터베이스 만들기
description: Azure 리소스 관리자 템플릿을 사용하여 Azure 데이터 탐색기 클러스터 및 데이터베이스를 만드는 방법에 대해 알아봅니다.
author: orspod
ms.author: orspodek
ms.reviewer: lugoldbe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/26/2019
ms.openlocfilehash: 7a12f43c5c6685ea474f6f19f6e585b09b194fce
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81496499"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-an-azure-resource-manager-template"></a>Azure 리소스 관리자 템플릿을 사용하여 Azure 데이터 탐색기 클러스터 및 데이터베이스 만들기

> [!div class="op_single_selector"]
> * [포털](create-cluster-database-portal.md)
> * [CLI](create-cluster-database-cli.md)
> * [PowerShell](create-cluster-database-powershell.md)
> * [C#](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
> * [Azure Resource Manager 템플릿](create-cluster-database-resource-manager.md)

Azure 데이터 탐색기는 로그 및 원격 분석 데이터에 사용 가능한 빠르고 확장성이 우수한 데이터 탐색 서비스입니다. Azure Data Explorer를 사용하려면 먼저 클러스터를 만들고 이 클러스터에 데이터베이스를 하나 이상 만듭니다. 그런 다음, 데이터베이스에 대해 쿼리를 실행할 수 있도록 데이터베이스에 데이터를 수집(로드)합니다. 

이 문서에서는 Azure [리소스 관리자 템플릿을](/azure/azure-resource-manager/management/overview)사용하여 Azure 데이터 탐색기 클러스터 및 데이터베이스를 만듭니다. 또한 어떤 리소스를 배포할지 정의하는 방법 및 배포를 실행할 때 매개 변수를 지정하는 방법을 설명합니다. 배포를 위해 이 템플릿을 사용하거나 요구 사항에 맞게 사용자 지정을 할 수 있습니다. 템플릿 만들기에 대한 자세한 내용은 [Azure 리소스 관리자 템플릿 작성을](/azure/azure-resource-manager/resource-group-authoring-templates)참조하십시오. 템플릿에서 사용할 JSON 구문 및 속성은 [Microsoft.Kusto 리소스 유형을](/azure/templates/microsoft.kusto/allversions)참조하십시오.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="azure-resource-manager-template-for-cluster-and-database-creation"></a>클러스터 및 데이터베이스 생성을 위한 Azure 리소스 관리자 템플릿

이 문서에서는 기존 [빠른 시작 템플릿을](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-kusto-cluster-database/azuredeploy.json) 사용합니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "clusters_kustocluster_name": {
          "type": "string",
          "defaultValue": "[concat('kusto', uniqueString(resourceGroup().id))]",
          "metadata": {
            "description": "Name of the cluster to create"
          }
      },
      "databases_kustodb_name": {
          "type": "string",
          "defaultValue": "kustodb",
          "metadata": {
            "description": "Name of the database to create"
          }
      },
      "location": {
        "type": "string",
        "defaultValue": "[resourceGroup().location]",
        "metadata": {
          "description": "Location for all resources."
        }
      }
  },
  "variables": {},
  "resources": [
      {
          "name": "[parameters('clusters_kustocluster_name')]",
          "type": "Microsoft.Kusto/clusters",
          "sku": {
              "name": "Standard_D13_v2",
              "tier": "Standard",
              "capacity": 2
          },
          "apiVersion": "2019-09-07",
          "location": "[parameters('location')]",
          "tags": {
            "Created By": "GitHub quickstart template"
          }
      },
      {
          "name": "[concat(parameters('clusters_kustocluster_name'), '/', parameters('databases_kustodb_name'))]",
          "type": "Microsoft.Kusto/clusters/databases",
          "apiVersion": "2019-09-07",
          "location": "[parameters('location')]",
          "dependsOn": [
              "[resourceId('Microsoft.Kusto/clusters', parameters('clusters_kustocluster_name'))]"
          ],
          "properties": {
              "softDeletePeriodInDays": 365,
              "hotCachePeriodInDays": 31
          }
      }
  ]
}
```

더 많은 샘플 템플릿은 [Azure 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/)에서 찾을 수 있습니다.

## <a name="deploy-the-template-and-verify-template-deployment"></a>템플릿 배포 및 템플릿 배포 확인

[Azure 포털을 사용하거나](#use-the-azure-portal-to-deploy-the-template-and-verify-template-deployment) [powershell을 사용하여](#use-powershell-to-deploy-the-template-and-verify-template-deployment)Azure 리소스 관리자 템플릿을 배포할 수 있습니다.

### <a name="use-the-azure-portal-to-deploy-the-template-and-verify-template-deployment"></a>Azure 포털을 사용하여 템플릿 배포 및 템플릿 배포 확인

1. 클러스터 및 데이터베이스를 만들려면 다음 단추를 사용하여 배포를 시작합니다. 마우스 오른쪽 단추로 클릭하고 **새 창에서 열기**를 선택하면 이 문서의 나머지 단계를 수행할 수 있습니다.

    [![Azure에 배포](media/create-cluster-database-resource-manager/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-kusto-cluster-database%2Fazuredeploy.json)

    **Azure에 배포** 단추를 선택하면 Azure Portal에서 배포 양식을 작성할 수 있는 페이지로 이동하게 됩니다.

    ![Deploy to Azure](media/create-cluster-database-resource-manager/deploy-2-azure.png)

    양식을 사용하여 [Azure 포털에서 템플릿을 편집하고 배포할](/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal#edit-and-deploy-the-template) 수 있습니다.

1. 기본 및 **설정** 섹션을 **완료합니다.** 고유한 클러스터 및 데이터베이스 이름을 선택합니다.
Azure 데이터 탐색기 클러스터 및 데이터베이스를 만드는 데 몇 분 정도 걸립니다.

1. 배포를 확인하려면 [Azure 포털에서](https://portal.azure.com) 리소스 그룹을 열어 새 클러스터 및 데이터베이스를 찾습니다. 

### <a name="use-powershell-to-deploy-the-template-and-verify-template-deployment"></a>powershell을 사용하여 템플릿 배포 및 템플릿 배포 확인

#### <a name="deploy-the-template-using-powershell"></a>파워셸을 사용하여 템플릿 배포

1. 다음 코드 블록에서 **사용해보기**를 선택한 다음, 지침에 따라 Azure Cloud 셸에 로그인합니다.

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $resourceGroupName = "${projectName}rg"
    $clusterName = "${projectName}cluster"
    $parameters = @{}
    $parameters.Add("clusters_kustocluster_name", $clusterName)
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-kusto-cluster-database/azuredeploy.json"
    New-AzResourceGroup -Name $resourceGroupName -Location $location
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -TemplateParameterObject $parameters
    Write-Host "Press [ENTER] to continue ..."
    ```

1. **복사**를 선택하여 PowerShell 스크립트를 복사합니다.
1. 셸 콘솔 창을 마우스 오른쪽 단추로 클릭하고 **붙여넣기**를 선택합니다.
Azure 데이터 탐색기 클러스터 및 데이터베이스를 만드는 데 몇 분 정도 걸립니다.

#### <a name="verify-the-deployment-using-powershell"></a>PowerShell을 사용하여 배포 확인

배포를 확인하려면 다음 Azure PowerShell 스크립트를 사용합니다.  클라우드 셸이 여전히 열려 있으면 첫 번째 줄(읽기-호스트)을 복사/실행할 필요가 없습니다. PowerShell에서 Azure 데이터 탐색기 리소스 관리에 대한 자세한 내용은 [Az.Kusto](/powershell/module/az.kusto/?view=azps-2.7.0)을 참조하십시오. 

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that you used in the last procedure"

Install-Module -Name Az.Kusto
$resourceGroupName = "${projectName}rg"
$clusterName = "${projectName}cluster"

Get-AzKustoCluster -ResourceGroupName $resourceGroupName -Name $clusterName
Write-Host "Press [ENTER] to continue ..."
```

[!INCLUDE [data-explorer-clean-resources](includes/data-explorer-clean-resources.md)]

## <a name="next-steps"></a>다음 단계

[Azure 데이터 탐색기 클러스터 및 데이터베이스에 데이터 수집](ingest-data-overview.md)
