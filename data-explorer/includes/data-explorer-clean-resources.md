---
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 11/28/2019
ms.author: orspodek
ms.openlocfilehash: d55077b5d1938caf6df49d34e68ece7e32c56f85
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87350073"
---
## <a name="clean-up-resources"></a>리소스 정리

Azure 리소스가 더 이상 필요하지 않은 경우 리소스 그룹을 삭제하여 배포한 리소스를 정리합니다. 

### <a name="clean-up-resources-using-the-azure-portal"></a>Azure Portal를 사용 하 여 리소스 정리

[리소스 정리](../create-cluster-database-portal.md#clean-up-resources)의 단계에 따라 Azure Portal에서 리소스를 삭제 합니다.

### <a name="clean-up-resources-using-powershell"></a>PowerShell을 사용하여 리소스 정리

Cloud Shell이 아직 열려 있으면 첫 번째 줄(Read-Host)을 복사/실행할 필요가 없습니다.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that you used in the last procedure"
$resourceGroupName = "${projectName}rg"

Remove-AzResourceGroup -ResourceGroupName $resourceGroupName

Write-Host "Press [ENTER] to continue ..."
```