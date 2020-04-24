---
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 03/25/2020
ms.author: orspodek
ms.openlocfilehash: 081ba777f6ab19be774f127383e359ff761e7f0e
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81496993"
---
## <a name="create-a-new-key-vault"></a>새 키 자격 증명 모음 만들기

PowerShell을 사용하여 새 키 자격 증명 모음을 만들려면 [New-AzKeyVault](/powershell/module/az.keyvault/new-azkeyvault)를 호출합니다. Azure Data Explorer 암호화에 대한 고객 관리 키를 저장하는 데 사용하는 키 자격 증명 모음에는 두 가지 키 보호 설정인 **소프트 삭제** 및 제거 **안 함**이 설정되어 있어야 합니다. 아래 예제에서 괄호 안의 자리 표시자 값을 사용자 고유의 값으로 바꿉습니다.

```azurepowershell-interactive
$keyVault = New-AzKeyVault -Name <key-vault> `
    -ResourceGroupName <resource_group> `
    -Location <location> `
    -EnableSoftDelete `
    -EnablePurgeProtection
```

## <a name="configure-the-key-vault-access-policy"></a>키 자격 증명 모음 액세스 정책 구성

그런 다음 클러스터에 액세스할 수 있는 권한이 있도록 키 자격 증명 모음에 대한 액세스 정책을 구성합니다. 이 단계에서는 이전에 클러스터에 할당한 시스템 할당관리 ID를 사용합니다. 키 자격 증명 모음에 대한 액세스 정책을 설정하려면 [Set-AzKeyVaultAccessPolicy를](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy)호출합니다. 괄호의 자리 표시자 값을 고유한 값으로 바꾸고 이전 예제에 정의된 변수를 사용합니다.

```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy `
    -VaultName $keyVault.VaultName `
    -ObjectId $cluster.Identity.PrincipalId `
    -PermissionsToKeys wrapkey,unwrapkey,get,recover
```

## <a name="create-a-new-key"></a>새 키 만들기

다음으로 키 자격 증명 모음에 새 키를 만듭니다. 새 키를 만들려면 [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey)를 호출합니다. 괄호의 자리 표시자 값을 고유한 값으로 바꾸고 이전 예제에 정의된 변수를 사용합니다.

```azurepowershell-interactive
$key = Add-AzKeyVaultKey -VaultName $keyVault.VaultName -Name <key> -Destination 'Software'
```
