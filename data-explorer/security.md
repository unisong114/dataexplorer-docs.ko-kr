---
title: Azure에서 Azure 데이터 탐색기 클러스터 보안
description: Azure 데이터 탐색기에서 클러스터를 보호하는 방법에 대해 알아봅니다.
author: saguiitay
ms.author: itsagui
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: 786950011f10e25d6bcb72061212c1878e79d45a
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81495446"
---
# <a name="secure-azure-data-explorer-clusters-in-azure"></a>Azure에서 Azure 데이터 탐색기 클러스터 보안

이 문서에서는 클라우드에서 데이터와 리소스를 보호하고 비즈니스의 보안 요구 사항을 충족하는 데 도움이 되는 Azure Data Explorer의 보안에 대한 소개를 제공합니다. 클러스터를 안전하게 유지하는 것이 중요합니다. 클러스터 보안에는 보안 액세스 및 저장소를 포함하는 하나 이상의 Azure 기능이 포함됩니다. 이 문서에서는 클러스터를 안전하게 유지하는 데 도움이 되는 정보를 제공합니다.

## <a name="managed-identities-for-azure-resources"></a>Azure 리소스에 대한 관리 ID

클라우드 응용 프로그램을 빌드할 때 일반적인 과제는 클라우드 서비스에 대한 인증을 위한 코드의 자격 증명 관리입니다. 자격 증명을 안전하게 보호하는 것이 중요합니다. 자격 증명은 개발자 워크스테이션에 저장하거나 소스 제어에 체크 인해서는 안 됩니다. Azure Key Vault를 사용하면 자격 증명, 비밀 및 기타 키를 안전하게 저장할 수 있지만, 자격 증명/키/비밀을 검색하려면 코드가 Key Vault에 인증해야 합니다.

Azure 리소스 기능에 대한 Azure Active Directory(Azure AD) 관리 ID가 이 문제를 해결합니다. 이 기능은 Azure AD에서 자동으로 관리되는 ID를 Azure 서비스에 제공합니다. 이 ID를 사용하면 Key Vault를 비롯하여 Azure AD 인증을 지원하는 모든 서비스에 인증할 수 있으므로 코드에 자격 증명이 필요 없습니다. 이 서비스에 대한 자세한 내용은 Azure 리소스 개요 페이지에 [대한 관리ID를](/azure/active-directory/managed-identities-azure-resources/overview) 참조하세요.

## <a name="data-encryption"></a>데이터 암호화.

### <a name="azure-disk-encryption"></a>Azure 디스크 암호화

[Azure 디스크 암호화를](/azure/security/azure-security-disk-encryption-overview) 사용하면 조직 보안 및 규정 준수 약정을 충족하기 위해 데이터를 보호하고 보호할 수 있습니다. 클러스터의 가상 시스템의 OS 및 데이터 디스크에 대한 볼륨 암호화를 제공합니다. 또한 Azure Disk 암호화는 [Azure Key Vault와](/azure/key-vault/)통합되어 디스크 암호화 키 및 비밀을 제어 및 관리하고 VM 디스크의 모든 데이터가 암호화되도록 할 수 있습니다. 

### <a name="customer-managed-keys-with-azure-key-vault"></a>Azure Key Vault를 사용하는 고객 관리형 키

기본적으로 데이터는 Microsoft 에서 관리하는 키로 암호화됩니다. 암호화 키를 추가로 제어하려면 데이터 암호화에 사용할 고객 관리 키를 제공할 수 있습니다. 자체 키로 저장소 수준에서 데이터 암호화를 관리할 수 있습니다. 고객 관리 키는 모든 데이터를 암호화하고 해독하는 데 사용되는 루트 암호화 키에 대한 액세스를 보호하고 제어하는 데 사용됩니다. 고객 관리 키는 액세스 제어를 생성, 회전, 비활성화 및 해지할 수 있는 유연성을 제공합니다. 데이터를 보호하는 데 사용되는 암호화 키를 감사할 수도 있습니다.

Azure 키 자격 증명 모음을 사용하여 고객 관리 키를 저장합니다. 사용자 고유의 키를 만들어 키 자격 증명 모음에 저장하거나 Azure Key Vault API를 사용하여 키를 생성할 수 있습니다. Azure 데이터 탐색기 클러스터와 Azure 키 자격 증명 모음은 동일한 지역에 있어야 하지만 다른 구독에 있을 수 있습니다. Azure 키 자격 증명 모음에 대한 자세한 내용은 [Azure 키 자격 증명 모음이란 무엇입니까?](/azure/key-vault/key-vault-overview) 고객 관리 키에 대한 자세한 설명은 [Azure Key Vault를 통해 고객 관리 키를](/azure/storage/common/storage-service-encryption)참조하십시오. [C#](/azure/data-explorer/customer-managed-keys-csharp) 또는 [Azure 리소스 관리자 템플릿을](/azure/data-explorer/customer-managed-keys-resource-manager) 사용하여 Azure 데이터 탐색기 클러스터에서 고객 관리 키 구성

> [!Note]
> 고객 관리 키는 Azure Active Directory(Azure AD)의 기능인 Azure 리소스에 대해 관리되는 ID를 사용합니다. Azure 포털에서 고객 관리 키를 구성하려면 Azure Data Explorer 클러스터에 대한 [관리되는 ID 구성에](/azure/data-explorer/managed-identities)자세히 설명된 대로 **SystemAssigned** 관리되는 ID를 클러스터에 구성해야 합니다.

#### <a name="store-customer-managed-keys-in-azure-key-vault"></a>Azure 키 볼트에 고객 관리 키 저장

클러스터에서 고객 관리 키를 활성화하려면 Azure Key Vault를 사용하여 키를 저장합니다. 키 자격 증명 모음에서 **소프트 삭제** 및 삭제 **안 함을** 모두 사용하도록 설정해야 합니다. 키 자격 증명 모음은 클러스터와 동일한 구독에 있어야 합니다. Azure Data Explorer는 Azure 리소스에 대해 관리되는 ID를 사용하여 암호화 및 암호 해독 작업에 대한 키 자격 증명을 인증합니다. 관리되는 ID는 상호 디렉터리 시나리오를 지원하지 않습니다.

#### <a name="rotate-customer-managed-keys"></a>고객 관리 키 회전

규정 준수 정책에 따라 Azure Key Vault에서 고객 관리 키를 회전할 수 있습니다. 키가 회전되면 새 키 URI를 사용하도록 클러스터를 업데이트해야 합니다. 키를 회전해도 클러스터의 데이터 재암호화가 트리거되지 않습니다. 

#### <a name="revoke-access-to-customer-managed-keys"></a>고객 관리 키에 대한 액세스 취소

고객 관리 키에 대한 액세스를 취소하려면 PowerShell 또는 Azure CLI를 사용합니다. 자세한 내용은 [Azure 키 볼트 PowerShell](/powershell/module/az.keyvault/) 또는 Azure 키 볼트 [CLI를](/cli/azure/keyvault)참조하십시오. 암호화 키는 결과적으로 Azure Data Explorer에서 액세스할 수 없으므로 액세스 취소는 클러스터의 저장소 수준에 있는 모든 데이터에 대한 액세스를 차단합니다.

> [!Note]
> Azure Data Explorer에서 고객 관리 키에 대한 액세스가 해지되는 것을 식별하면 캐시된 데이터를 삭제하기 위해 클러스터를 자동으로 일시 중단합니다. 키에 대한 액세스가 반환되면 클러스터를 수동으로 다시 시작해야 합니다.

## <a name="role-based-access-control"></a>역할 기반 액세스 제어

[RBAC(역할 기반 액세스 제어)를](/azure/role-based-access-control/overview)사용하면 팀 내에서 업무를 분리하고 클러스터 사용자에게 필요한 액세스 권한만 부여할 수 있습니다. 클러스터에 대한 무제한 권한을 모두에게 부여하는 대신 특정 작업만 허용할 수 있습니다. Azure [CLI](/azure/role-based-access-control/role-assignments-cli)또는 Azure PowerShell 을 사용하여 [Azure 포털의](/azure/role-based-access-control/role-assignments-portal) [데이터베이스에 대한 액세스 제어를](/azure/data-explorer/manage-database-permissions) [구성할](/azure/role-based-access-control/role-assignments-powershell)수 있습니다.

## <a name="next-steps"></a>다음 단계

* Azure 데이터 탐색기 - 미사용 암호화를 사용하도록 설정하여 [포털에서 클러스터를 보호합니다.](manage-cluster-security.md)
* [Azure 데이터 탐색기 클러스터에 대해 관리되는 ID 구성](managed-identities.md)
* [Azure 리소스 관리자 템플릿을 사용하여 고객 관리 키 구성](customer-managed-keys-resource-manager.md)
* [C를 사용하여 고객 관리 키 구성 #](customer-managed-keys-csharp.md)

