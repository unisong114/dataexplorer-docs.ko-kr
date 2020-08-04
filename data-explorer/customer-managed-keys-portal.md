---
title: Azure Portal를 사용 하 여 고객이 관리 하는 키 구성
description: 이 문서에서는 Azure 데이터 탐색기의 데이터에서 고객이 관리 하는 키 암호화를 구성 하는 방법을 설명 합니다.
author: orspod
ms.author: orspodek
ms.reviewer: itsagui
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/26/2020
ms.openlocfilehash: 4477aba7c661b0bc434d2bb0cc0a635603cc8ad4
ms.sourcegitcommit: d9fbcd6c9787f90de62e8e832c92d43b8090cbfc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/03/2020
ms.locfileid: "87515825"
---
# <a name="configure-customer-managed-keys-using-the-azure-portal"></a>Azure Portal를 사용 하 여 고객 관리 키 구성

> [!div class="op_single_selector"]
> * [포털](customer-managed-keys-portal.md)
> * [C#](customer-managed-keys-csharp.md)
> * [Azure Resource Manager 템플릿](customer-managed-keys-resource-manager.md)
> * [CLI](customer-managed-keys-cli.md)
> * [PowerShell](customer-managed-keys-powershell.md)

[!INCLUDE [data-explorer-configure-customer-managed-keys](includes/data-explorer-configure-customer-managed-keys.md)]

## <a name="enable-encryption-with-customer-managed-keys-in-the-azure-portal"></a>Azure Portal에서 고객이 관리 하는 키로 암호화 사용

이 문서에서는 Azure Portal를 사용 하 여 고객이 관리 하는 키 암호화를 사용 하도록 설정 하는 방법을 보여 줍니다. 기본적으로 Azure 데이터 탐색기 암호화는 Microsoft 관리 키를 사용 합니다. Azure 데이터 탐색기 클러스터가 고객이 관리 하는 키를 사용 하도록 구성 하 고 클러스터와 연결할 키를 지정 합니다.

1. [Azure Portal](https://portal.azure.com/)에서 [Azure 데이터 탐색기 클러스터](create-cluster-database-portal.md#create-a-cluster) 리소스로 이동 합니다. 
1. **Settings**  >  포털의 왼쪽 창에서 설정**암호화** 를 선택 합니다.
1. **암호화** 창에서 **고객이 관리** 하는 키 설정에 대해 **켜기** 를 선택 합니다.
1. **키 선택**을 클릭 합니다.

    ![고객 관리형 키 구성](media/customer-managed-keys-portal/cmk-encryption-setting.png)

1. **Azure Key Vault에서 키 선택** 창의 드롭다운 목록에서 기존 **키 자격 증명 모음** 을 선택 합니다. 새로 **만들기** 를 선택 하 여 [새 Key Vault을 만들려면](/azure/key-vault/quick-create-portal#create-a-vault) **Key Vault 만들기** 화면으로 라우팅됩니다.

1. **키**를 선택 합니다.
1. **버전**을 선택 합니다.
1. **선택**을 클릭합니다.

    ![Azure Key Vault에서 키 선택](media/customer-managed-keys-portal/cmk-key-vault.png)

1. 이제 키를 포함 하는 **암호화** 창에서 **저장**을 선택 합니다. CMK가 성공적으로 생성 되 면 **알림에서**성공 메시지가 표시 됩니다.

    ![고객 관리 키 저장](media/customer-managed-keys-portal/cmk-encryption-setting.png)

Azure 데이터 탐색기 클러스터에 대해 고객이 관리 하는 키를 사용 하도록 설정 하면 클러스터에 대 한 시스템 할당 id (있는 경우)가 생성 됩니다. 또한 선택한 Key Vault에서 Azure 데이터 탐색기 클러스터에 필요한 get, wrapKey 및 unwarpKey 권한을 제공 하 고 Key Vault 속성을 가져와야 합니다. 

> [!NOTE]
> 고객이 관리 하는 키를 만든 후 제거 하려면 **Off** 를 선택 합니다.

## <a name="next-steps"></a>다음 단계

* [Azure에서 Azure 데이터 탐색기 클러스터 보호](security.md)
* 미사용 암호화를 사용 하도록 설정 하 여 [Azure Azure Portal 데이터 탐색기에서 디스크 암호화를 사용 하 여 클러스터를 보호](cluster-disk-encryption.md) 합니다.
* [Azure Resource Manager 템플릿을 사용 하 여 고객 관리 키 구성](customer-managed-keys-resource-manager.md)
* [C를 사용 하 여 고객 관리 키 구성 #](customer-managed-keys-csharp.md)



