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
## <a name="create-a-new-key-vault"></a>새 key vault 만들기

PowerShell을 사용 하 여 새 키 자격 증명 모음을 만들려면 [AzKeyVault](/powershell/module/az.keyvault/new-azkeyvault)를 호출 합니다. Azure 데이터 탐색기 암호화를 위해 고객 관리 키를 저장 하는 데 사용 하는 key vault에는 두 가지 키 보호 설정, **일시 삭제** 및 **제거 안 함**이 있어야 합니다. 괄호 안의 자리 표시자 값을 아래 예제에서 사용자 고유의 값으로 바꿉니다.

```azurepowershell-interactive
$keyVault = New-AzKeyVault -Name <key-vault> `
    -ResourceGroupName <resource_group> `
    -Location <location> `
    -EnableSoftDelete `
    -EnablePurgeProtection
```

## <a name="configure-the-key-vault-access-policy"></a>키 자격 증명 모음 액세스 정책 구성

그런 다음 키 자격 증명 모음에 대 한 액세스 정책을 구성 하 여 클러스터에 액세스할 수 있는 권한을 부여 합니다. 이 단계에서는 이전에 클러스터에 할당 한 시스템 할당 관리 id를 사용 합니다. 키 자격 증명 모음에 대 한 액세스 정책을 설정 하려면 [AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy)를 호출 합니다. 대괄호의 자리 표시자 값을 사용자 고유의 값으로 바꾸고 앞의 예제에 정의 된 변수를 사용 합니다.

```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy `
    -VaultName $keyVault.VaultName `
    -ObjectId $cluster.Identity.PrincipalId `
    -PermissionsToKeys wrapkey,unwrapkey,get,recover
```

## <a name="create-a-new-key"></a>새 키 만들기

다음으로 키 자격 증명 모음에 새 키를 만듭니다. 새 키를 만들려면 [AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey)를 호출 합니다. 대괄호의 자리 표시자 값을 사용자 고유의 값으로 바꾸고 앞의 예제에 정의 된 변수를 사용 합니다.

```azurepowershell-interactive
$key = Add-AzKeyVaultKey -VaultName $keyVault.VaultName -Name <key> -Destination 'Software'
```
