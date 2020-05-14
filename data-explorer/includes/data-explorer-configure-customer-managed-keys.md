---
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 01/07/2020
ms.author: orspodek
ms.openlocfilehash: 5187f89a939daf45c0a5826e483de52cb7784103
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/13/2020
ms.locfileid: "83383866"
---
Azure 데이터 탐색기는 미사용 저장소 계정의 모든 데이터를 암호화 합니다. 기본적으로 데이터는 Microsoft 관리 키를 사용 하 여 암호화 됩니다. 암호화 키에 대 한 추가 제어를 위해 고객 관리 키를 제공 하 여 데이터 암호화에 사용할 수 있습니다. 

고객 관리 키는 [Azure Key Vault](/azure/key-vault/key-vault-overview)에 저장 되어야 합니다. 사용자 고유의 키를 만들어 키 자격 증명 모음에 저장 하거나 Azure Key Vault API를 사용 하 여 키를 생성할 수 있습니다. Azure 데이터 탐색기 클러스터와 key vault는 동일한 지역에 있어야 하지만 다른 구독에 있을 수 있습니다. 고객 관리 키에 대 한 자세한 설명은 [Azure Key Vault를 사용 하 여 고객 관리 키](/azure/storage/common/storage-service-encryption)를 참조 하세요. 

이 문서에서는 고객이 관리 하는 키를 구성 하는 방법을 보여 줍니다.

## <a name="configure-azure-key-vault"></a>Azure Key Vault 구성

Azure 데이터 탐색기을 사용 하 여 고객 관리 키를 구성 하려면 [키 자격 증명 모음에 대 한 두 가지 속성을 설정](/azure/key-vault/key-vault-ovw-soft-delete)해야 합니다. **일시 삭제** 및 **제거 안 함** 이러한 속성은 기본적으로 사용 되지 않습니다. 이러한 속성을 사용 하도록 설정 하려면 **일시 삭제를 사용 하도록 설정** 하 고 새 또는 기존 키 자격 증명 모음에서 [PowerShell](/azure/key-vault/key-vault-soft-delete-powershell) 또는 [Azure CLI](/azure/key-vault/key-vault-soft-delete-cli) 에서 **보호 제거** 를 사용 하도록 설정 합니다. 2048 크기의 RSA 키만 지원 됩니다. 키에 대 한 자세한 내용은 [Key Vault 키](/azure/key-vault/about-keys-secrets-and-certificates#key-vault-keys)를 참조 하세요.

> [!NOTE]
> 고객 관리 키를 사용 하는 데이터 암호화가 [리더 및 종동체 클러스터](../follower.md) 에서 지원 되지 않음

## <a name="assign-an-identity-to-the-cluster"></a>클러스터에 id 할당

클러스터에 대해 고객이 관리 하는 키를 사용 하도록 설정 하려면 먼저 시스템 할당 관리 되는 id를 클러스터에 할당 합니다. 이 관리 되는 id를 사용 하 여 클러스터에 키 자격 증명 모음에 액세스할 수 있는 권한을 부여 합니다. 시스템 할당 관리 id를 구성 하려면 [관리 되는 id](../managed-identities.md)를 참조 하십시오.
