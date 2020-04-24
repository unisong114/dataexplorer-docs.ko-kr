---
title: Azure 리소스 관리자 템플릿을 사용하여 Azure 데이터 탐색기에서 고객 관리 키 구성
description: 이 문서에서는 Azure 리소스 관리자 템플릿을 사용하여 Azure Data Explorer의 데이터에 대한 고객 관리 키 암호화를 구성하는 방법을 설명합니다.
author: saguiitay
ms.author: itsagui
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: 1af7404a20c7246b50f80385f666da886f0cc96b
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81496967"
---
# <a name="configure-customer-managed-keys-using-the-azure-resource-manager-template"></a>Azure 리소스 관리자 템플릿을 사용하여 고객 관리 키 구성

> [!div class="op_single_selector"]
> * [포털](customer-managed-keys-portal.md)
> * [C#](customer-managed-keys-csharp.md)
> * [Azure Resource Manager 템플릿](customer-managed-keys-resource-manager.md)

[!INCLUDE [data-explorer-configure-customer-managed-keys](includes/data-explorer-configure-customer-managed-keys.md)]

[!INCLUDE [data-explorer-configure-customer-managed-keys part 2](includes/data-explorer-configure-customer-managed-keys-b.md)]

## <a name="configure-encryption-with-customer-managed-keys"></a>고객 관리 키로 암호화 구성

이 섹션에서는 Azure 리소스 관리자 템플릿을 사용하여 고객 관리 키를 구성합니다. 기본적으로 Azure 데이터 탐색기 암호화는 Microsoft에서 관리하는 키를 사용합니다. 이 단계에서는 Azure Data Explorer 클러스터를 구성하여 고객 관리 키를 사용하고 클러스터와 연결할 키를 지정합니다.

Azure 포털을 사용하거나 PowerShell을 사용하여 Azure 리소스 관리자 템플릿을 배포할 수 있습니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "clusterName": {
      "type": "string",
      "defaultValue": "[concat('kusto', uniqueString(resourceGroup().id))]",
      "metadata": {
        "description": "Name of the cluster to create"
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
      "name": "[parameters('clusterName')]",
      "type": "Microsoft.Kusto/clusters",
      "sku": {
        "name": "Standard_D13_v2",
        "tier": "Standard",
        "capacity": 2
      },
      "apiVersion": "2019-09-07",
      "location": "[parameters('location')]",
      "properties": {
        "keyVaultProperties": {
          "keyVaultUri": "<keyVaultUri>",
          "keyName": "<keyName>",
          "keyVersion": "<keyVersion"
        }
      }
    }
  ]
}
```

## <a name="update-the-key-version"></a>키 버전 업데이트

키의 새 버전을 만들 때 새 버전을 사용 하려면 클러스터를 업데이트 해야 합니다. 먼저 최신 `Get-AzKeyVaultKey` 버전의 키를 얻으려면 호출합니다. 그런 다음 클러스터의 키 자격 증명 모음 속성을 업데이트하여 [고객이 관리하는 키로 암호화 구성에](#configure-encryption-with-customer-managed-keys)표시된 대로 키의 새 버전을 사용합니다.

## <a name="next-steps"></a>다음 단계

* [Azure에서 Azure 데이터 탐색기 클러스터 보안](security.md)
* [Azure 데이터 탐색기 클러스터에 대해 관리되는 ID 구성](managed-identities.md)
* Azure 데이터 탐색기 - 미사용 암호화를 사용하도록 설정하여 [Azure 포털에서 클러스터를 보호합니다.](manage-cluster-security.md)
* [C를 사용하여 고객 관리 키 구성 #](customer-managed-keys-csharp.md)

