---
title: PowerShell을 사용 하 여 고객 관리 키 구성
description: 이 문서에서는 PowerShell을 사용 하 여 Azure 데이터 탐색기의 데이터에서 고객이 관리 하는 키 암호화를 구성 하는 방법을 설명 합니다.
author: orspod
ms.author: orspodek
ms.reviewer: roshauli
ms.service: data-explorer
ms.topic: how-to
ms.date: 06/04/2020
ms.openlocfilehash: 5c52253a6d8c4978931aeff3a5a5a73367f14ce5
ms.sourcegitcommit: f354accde64317b731f21e558c52427ba1dd4830
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88871998"
---
# <a name="configure-customer-managed-keys-using-powershell"></a>PowerShell을 사용 하 여 고객 관리 키 구성

> [!div class="op_single_selector"]
> * [포털](customer-managed-keys-portal.md)
> * [C#](customer-managed-keys-csharp.md)
> * [Azure Resource Manager 템플릿](customer-managed-keys-resource-manager.md)
> * [CLI](customer-managed-keys-cli.md)
> * [PowerShell](customer-managed-keys-powershell.md)

[!INCLUDE [data-explorer-configure-customer-managed-keys](includes/data-explorer-configure-customer-managed-keys.md)]

## <a name="enable-encryption-with-customer-managed-keys-using-powershell"></a>PowerShell을 사용 하 여 고객이 관리 하는 키로 암호화 사용

이 문서에서는 PowerShell을 사용 하 여 고객이 관리 하는 키 암호화를 사용 하도록 설정 하는 방법을 보여 줍니다. 기본적으로 Azure 데이터 탐색기 암호화는 Microsoft 관리 키를 사용 합니다. Azure 데이터 탐색기 클러스터가 고객이 관리 하는 키를 사용 하도록 구성 하 고 클러스터와 연결할 키를 지정 합니다.

1. 다음 명령을 실행하여 Azure에 로그인합니다.

    ```azurepowershell-interactive
    Connect-AzAccount
    ```

1. 클러스터가 등록 된 구독을 설정 합니다.

    ```azurepowershell-interactive
    Set-AzContext -SubscriptionId "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    ```

1. 다음 명령을 실행 하 여 새 키를 설정 합니다.

    ```azurepowershell-interactive
    Update-AzKustoCluster -ResourceGroupName "mytestrg" -Name "mytestcluster" -KeyVaultPropertyKeyName "<key-name>" -KeyVaultPropertyKeyVaultUri "<key-vault-uri>" -KeyVaultPropertyKeyVersion "<key-version>"
    ```

1. 다음 명령을 실행 하 고 ' KeyVaultProperty '를 확인 합니다. 클러스터가 성공적으로 업데이트 되었는지 확인 하는 속성입니다.

    ```azurepowershell-interactive
    Get-AzKustoCluster -Name "mytestcluster" -ResourceGroupName "mytestrg" | Format-List
    ```
