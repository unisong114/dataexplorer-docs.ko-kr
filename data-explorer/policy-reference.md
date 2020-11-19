---
title: Azure 데이터 탐색기에 대 한 기본 제공 정책 정의
description: Azure 데이터 탐색기에 대 한 Azure Policy 기본 제공 정책 정의를 나열 합니다. 이러한 기본 제공 정책 정의는 Azure 리소스를 관리하는 일반적인 방법을 제공합니다.
ms.date: 11/17/2020
ms.topic: reference
author: orspod
ms.author: orspodek
ms.service: data-explorer
ms.custom: subject-policy-reference
ms.openlocfilehash: 9f2e98a1be83be659c8f7b85283c868d96f999eb
ms.sourcegitcommit: 0820454feb02ae489f3a86b688690422ae29d788
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/19/2020
ms.locfileid: "94937927"
---
# <a name="azure-policy-built-in-definitions-for-azure-data-explorer"></a>Azure 데이터 탐색기에 대 한 기본 제공 정의 Azure Policy

이 페이지는 Azure 데이터 탐색기에 대 한 [Azure Policy](/azure/governance/policy/overview) 기본 제공 정책 정의의 인덱스입니다. 다른 서비스에 대한 추가 Azure Policy 기본 제공 기능은 [Azure Policy 기본 제공 정의](/azure/governance/policy/samples/built-in-policies)를 참조하세요.

Azure Portal의 정책 정의에 대한 각 기본 제공 정책 정의 링크의 이름입니다. **Version** 열의 링크를 사용하여 [Azure Policy GitHub 리포지토리](https://github.com/Azure/azure-policy)에서 원본을 봅니다.

## <a name="azure-data-explorer"></a>Azure Data Explorer

|Name<br /><sub>(Azure Portal)</sub> |Description |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[휴지 상태의 Azure 데이터 탐색기 암호화는 고객 관리 키를 사용 해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F81e74cea-30fd-40d5-802f-d72103c2aaaa) |Azure 데이터 탐색기 클러스터에서 고객 관리 키를 사용 하 여 미사용 암호화를 사용 하면 미사용 암호화에서 사용 되는 키에 대 한 추가 제어 기능을 제공 합니다. 이 기능은 특별 한 규정 준수 요구 사항이 있는 고객에 게 적용 되며, 키를 관리 하는 Key Vault 필요 합니다. |감사, 거부, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Data%20Explorer/ADX_CMK.json) |
|[Azure 데이터 탐색기에서 디스크 암호화를 사용 하도록 설정 해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff4b53539-8df9-40e4-86c6-6b607703bd4e) |디스크 암호화를 사용 하면 조직의 보안 및 규정 준수 약정에 맞게 데이터를 보호 하 고 보호할 수 있습니다. |감사, 거부, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Data%20Explorer/ADX_disk_encrypted.json) |
|[Azure 데이터 탐색기에서 이중 암호화를 사용 하도록 설정 해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fec068d99-e9c7-401f-8cef-5bdde4e6ccf1) |이중 암호화를 사용 하면 조직의 보안 및 규정 준수 약정에 맞게 데이터를 보호 하 고 보호할 수 있습니다. 이중 암호화가 사용 하도록 설정 된 경우 저장소 계정의 데이터는 두 개의 서로 다른 암호화 알고리즘과 두 개의 다른 키를 사용 하 여 서비스 수준에서 한 번, 인프라 수준에서 한 번, 두 번 암호화 됩니다. |감사, 거부, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Data%20Explorer/ADX_doubleEncryption.json) |
|[Azure 데이터 탐색기에 대 한 가상 네트워크 주입을 사용 하도록 설정 해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9ad2fd1f-b25f-47a2-aa01-1a5a779e6413) |네트워크 보안 그룹 규칙을 적용 하 고 온-프레미스에 연결 하 고 데이터 연결 원본에 서비스 끝점을 보호할 수 있는 virtual network 주입을 사용 하 여 네트워크 경계를 보호 합니다. |감사, 거부, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Data%20Explorer/ADX_VNET_configured.json) |

## <a name="next-steps"></a>다음 단계

- [Azure Policy GitHub 리포지토리](https://github.com/Azure/azure-policy)의 기본 제공 기능을 참조하세요.
- [Azure Policy 정의 구조](/azure/governance/policy/concepts/definition-structure)를 검토합니다.
- [정책 효과 이해](/azure/governance/policy/concepts/effects)를 검토합니다.
