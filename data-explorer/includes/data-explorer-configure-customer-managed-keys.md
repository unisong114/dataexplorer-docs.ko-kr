---
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 01/07/2020
ms.author: orspodek
ms.openlocfilehash: 7f5c02c6c009e8916ed063454e0ae6049892e95c
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81496980"
---
Azure Data Explorer는 미사용 저장소 계정의 모든 데이터를 암호화합니다. 기본적으로 데이터는 Microsoft 에서 관리하는 키로 암호화됩니다. 암호화 키를 추가로 제어하려면 데이터 암호화에 사용할 고객 관리 키를 제공할 수 있습니다. 

고객 관리 키는 [Azure 키 자격 증명 모음에](/azure/key-vault/key-vault-overview)저장되어야 합니다. 사용자 고유의 키를 만들어 키 자격 증명 모음에 저장하거나 Azure Key Vault API를 사용하여 키를 생성할 수 있습니다. Azure 데이터 탐색기 클러스터와 키 자격 증명 모음은 동일한 지역에 있어야 하지만 다른 구독에 있을 수 있습니다. 고객 관리 키에 대한 자세한 설명은 [Azure Key Vault를 통해 고객 관리 키를](/azure/storage/common/storage-service-encryption)참조하십시오. 

이 문서에서는 고객 관리 키를 구성하는 방법을 보여 주며, 이 문서에서는

## <a name="configure-azure-key-vault"></a>Azure Key Vault 구성

Azure Data Explorer를 사용하여 고객 관리 키를 구성하려면 키 자격 증명 **Do Not Purge** [모음에 두 가지 속성을 설정해야](/azure/key-vault/key-vault-ovw-soft-delete) **합니다.** 이러한 속성은 기본적으로 활성화되지 않습니다. 이러한 속성을 사용하려면 새 키 또는 기존 키 자격 증명 모음에서 [PowerShell](/azure/key-vault/key-vault-soft-delete-powershell) 또는 [Azure CLI에서](/azure/key-vault/key-vault-soft-delete-cli) **일시 삭제 활성화** 및 퍼지 보호 **활성화를** 수행합니다. 크기 2048의 RSA 키만 지원됩니다. 키에 대한 자세한 내용은 [키 볼트 키를](/azure/key-vault/about-keys-secrets-and-certificates#key-vault-keys)참조하십시오.

> [!NOTE]
> 고객 관리 키를 사용한 데이터 암호화는 [리더 및 팔로워 클러스터에서](/azure/data-explorer/follower)지원되지 않습니다. 

## <a name="assign-an-identity-to-the-cluster"></a>클러스터에 ID 할당

클러스터에 대해 고객 관리 키를 활성화하려면 먼저 시스템에 할당된 관리되는 ID를 클러스터에 할당합니다. 이 관리되는 ID를 사용하여 키 자격 증명 모음에 액세스할 수 있는 클러스터 권한을 부여합니다. 시스템 할당된 관리 ID를 구성하려면 [관리되는 ID를](/azure/data-explorer/managed-identities)참조하십시오.