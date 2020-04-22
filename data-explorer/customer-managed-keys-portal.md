---
title: Azure 포털을 사용하여 고객 관리 키 구성
description: 이 문서에서는 Azure Data Explorer의 데이터에 대해 고객 관리 키 암호화를 구성하는 방법을 설명합니다.
author: orspod
ms.author: orspodek
ms.reviewer: itsagui
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/26/2020
ms.openlocfilehash: 13ad30e53ff38e627f44bac96010ae6d21902cee
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81497747"
---
# <a name="configure-customer-managed-keys-using-the-azure-portal"></a>Azure 포털을 사용하여 고객 관리 키 구성

> [!div class="op_single_selector"]
> * [포털](customer-managed-keys-portal.md)
> * [C#](customer-managed-keys-csharp.md)
> * [Azure Resource Manager 템플릿](customer-managed-keys-resource-manager.md)

[!INCLUDE [data-explorer-configure-customer-managed-keys](includes/data-explorer-configure-customer-managed-keys.md)]

## <a name="enable-encryption-with-customer-managed-keys-in-the-azure-portal"></a>Azure 포털에서 고객 관리 키로 암호화 를 사용하도록 설정

이 문서에서는 Azure 포털을 사용하여 고객 관리 키 암호화를 사용하도록 설정하는 방법을 보여 주며 있습니다. 기본적으로 Azure 데이터 탐색기 암호화는 Microsoft에서 관리하는 키를 사용합니다. Azure Data Explorer 클러스터를 구성하여 고객 관리 키를 사용하고 클러스터와 연결할 키를 지정합니다.

1. Azure [포털에서](https://portal.azure.com/) [Azure 데이터 탐색기 클러스터 리소스로](create-cluster-database-portal.md#create-a-cluster) 이동합니다. 
1. 포털의 왼쪽 창에서 **설정** > **암호화를** 선택합니다.
1. **암호화** 창에서 **고객이 관리하는 키** 설정에 대해 **켜기(On)를** 선택합니다.
1. **키 선택 을 클릭합니다.**

    ![고객 관리형 키 구성](media/customer-managed-keys-portal/cmk-encryption-setting.png)

1. Azure **키 볼트 창에서 키 선택 창에서** 드롭다운 목록에서 기존 **키 자격 증명 모음을** 선택합니다. [새 키 볼트를 만들려면](/azure/key-vault/quick-create-portal#create-a-vault) **새 만들기를** 선택하면 **키 볼트 만들기** 화면으로 라우팅됩니다.

1. **키**를 선택합니다.
1. **버전**을 선택합니다.
1. **선택**을 클릭합니다.

    ![Azure 키 볼트에서 키 선택](media/customer-managed-keys-portal/cmk-key-vault.png)

1. 이제 키가 포함된 **암호화** 창에서 **저장을**선택합니다. CMK 생성이 성공하면 **알림**에 성공 메시지가 표시됩니다.

    ![고객 관리 키 저장](media/customer-managed-keys-portal/cmk-encryption-setting.png)

Azure Data Explorer 클러스터에 대해 고객 관리 키를 사용하도록 설정하면 클러스터에 할당된 ID가 존재하지 않는 경우 시스템에 할당된 ID를 생성하게 됩니다. 또한 선택한 키 볼트에서 Azure 데이터 탐색기 클러스터에 필요한 get, wrapKey 및 unwarpKey 권한을 제공하고 키 자격 증명 모음 속성을 가져옵니다. 

> [!NOTE]
> **끄기를** 선택하여 고객이 관리하는 키를 만든 후 제거합니다.

## <a name="next-steps"></a>다음 단계

* [Azure에서 Azure 데이터 탐색기 클러스터 보안](security.md)
* Azure 데이터 탐색기 - 미사용 암호화를 사용하도록 설정하여 [Azure 포털에서 클러스터를 보호합니다.](manage-cluster-security.md)
* [Azure 리소스 관리자 템플릿을 사용하여 고객 관리 키 구성](customer-managed-keys-resource-manager.md)
* [C를 사용하여 고객 관리 키 구성 #](customer-managed-keys-csharp.md)



