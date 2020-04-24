---
title: Azure 리소스 관리자 템플릿을 사용하여 Azure 데이터 탐색기의 데이터베이스 보안 주체 추가
description: 이 문서에서는 Azure 리소스 관리자 템플릿을 사용하여 Azure Data Explorer에 대한 데이터베이스 보안 주체를 추가하는 방법을 알아봅니다.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 02/03/2020
ms.openlocfilehash: 80fcf4b6c5d64dde7bd1c5475fb6a3dbafd7f907
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81496434"
---
# <a name="add-database-principals-for-azure-data-explorer-by-using-an-azure-resource-manager-template"></a>Azure 리소스 관리자 템플릿을 사용하여 Azure 데이터 탐색기용 데이터베이스 보안 주체 추가

> [!div class="op_single_selector"]
> * [C#](database-principal-csharp.md)
> * [Python](database-principal-python.md)
> * [Azure Resource Manager 템플릿](database-principal-resource-manager.md)

Azure 데이터 탐색기는 로그 및 원격 분석 데이터에 사용 가능한 빠르고 확장성이 우수한 데이터 탐색 서비스입니다. 이 문서에서는 Azure 리소스 관리자 템플릿을 사용하여 Azure 데이터 탐색기의 데이터베이스 보안 주체를 추가합니다.

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독이 아직 없는 경우 시작하기 전에 [Azure 체험 계정](https://azure.microsoft.com/free/)을 만듭니다.
* [클러스터 및 데이터베이스](create-cluster-database-portal.md) 만들기

## <a name="azure-resource-manager-template-for-adding-a-database-principal"></a>데이터베이스 보안 주체를 추가하기 위한 Azure 리소스 관리자 템플릿

다음 예제에서는 데이터베이스 보안 주체를 추가하기 위한 Azure 리소스 관리자 템플릿을 보여 주며 있습니다.  양식을 사용하여 [Azure 포털에서 템플릿을 편집하고 배포할](/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal#edit-and-deploy-the-template) 수 있습니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "principalAssignmentName": {
            "type": "string",
            "defaultValue": "principalAssignment1",
            "metadata": {
                "description": "Specifies the name of the principal assignment"
            }
        },
        "clusterName": {
            "type": "string",
            "defaultValue": "mykustocluster",
            "metadata": {
                "description": "Specifies the name of the cluster"
            }
        },
        "databaseName": {
            "type": "string",
            "defaultValue": "mykustodatabase",
            "metadata": {
                "description": "Specifies the name of the database"
            }
        },
        "principalIdForDatabase": {
            "type": "string",
            "metadata": {
                "description": "Specifies the principal id. It can be user email, application (client) ID, security group name"
            }
        },
        "roleForDatabasePrincipal": {
            "type": "string",
            "defaultValue": "Admin",
            "metadata": {
                "description": "Specifies the database principal role. It can be 'Admin', 'Ingestor', 'Monitor', 'User', 'UnrestrictedViewers', 'Viewer'"
            }
        },
        "tenantIdForDatabasePrincipal": {
            "type": "string",
            "metadata": {
                "description": "Specifies the tenantId of the database principal"
            }
        },
        "principalTypeForDatabase": {
            "type": "string",
            "defaultValue": "App",
            "metadata": {
                "description": "Specifies the principal type. It can be 'User', 'App', 'Group'"
            }
        }
    },
    "variables": {
    },
    "resources": [{
            "type": "Microsoft.Kusto/Clusters/Databases/principalAssignments",
            "apiVersion": "2019-11-09",
            "name": "[concat(parameters('clusterName'), '/', parameters('databaseName'), '/', parameters('principalAssignmentName'))]",
            "properties": {
                "principalId": "[parameters('principalIdForDatabase')]",
                "role": "[parameters('roleForDatabasePrincipal')]",
                "tenantId": "[parameters('tenantIdForDatabasePrincipal')]",
                "principalType": "[parameters('principalTypeForDatabase')]"
            }
        }
    ]
}
```

## <a name="next-steps"></a>다음 단계

* [Event Hub에서 Azure Data Explorer로 데이터 수집](ingest-data-event-hub.md)
