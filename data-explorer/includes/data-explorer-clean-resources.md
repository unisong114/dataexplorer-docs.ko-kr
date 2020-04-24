---
author: lucygoldbergmicrosoft
ms.service: data-explorer
ms.topic: include
ms.date: 11/28/2019
ms.author: lugoldbe
ms.openlocfilehash: de8a37b1b27dc1bff377c6212c0bfea0a13c7de2
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81496486"
---
## <a name="clean-up-resources"></a>리소스 정리

Azure 리소스가 더 이상 필요하지 않은 경우 리소스 그룹을 삭제하여 배포한 리소스를 정리합니다. 

### <a name="clean-up-resources-using-the-azure-portal"></a>Azure 포털을 사용하여 리소스 정리

리소스 정리 단계에 따라 Azure 포털의 [리소스를](../create-cluster-database-portal.md#clean-up-resources)삭제합니다.

### <a name="clean-up-resources-using-powershell"></a>PowerShell을 사용하여 리소스 정리

클라우드 셸이 여전히 열려 있으면 첫 번째 줄(읽기-호스트)을 복사/실행할 필요가 없습니다.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that you used in the last procedure"
$resourceGroupName = "${projectName}rg"

Remove-AzResourceGroup -ResourceGroupName $resourceGroupName

Write-Host "Press [ENTER] to continue ..."
```