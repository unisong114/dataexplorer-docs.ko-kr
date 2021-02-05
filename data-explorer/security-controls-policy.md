---
title: Azure Data Explorer에 대한 Azure Policy 규정 준수 컨트롤
description: Azure Data Explorer에 사용할 수 있는 Azure Policy 규정 준수 컨트롤을 나열합니다. 이러한 기본 제공 정책 정의는 Azure 리소스의 규정 준수를 관리하는 일반적인 방법을 제공합니다.
ms.date: 02/05/2021
ms.topic: sample
author: orspod
ms.author: orspodek
ms.service: data-explorer
ms.custom: subject-policy-compliancecontrols
ms.openlocfilehash: 9f52935e235dcf64c5236d3fbdd58b95441aa5c9
ms.sourcegitcommit: d1c2433df183d0cfbfae4d3b869ee7f9cbf00fe4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/05/2021
ms.locfileid: "99586391"
---
# <a name="azure-policy-regulatory-compliance-controls-for-azure-data-explorer"></a>Azure Data Explorer에 대한 Azure Policy 규정 준수 컨트롤

[Azure Policy의 규정 준수](/azure/governance/policy/concepts/regulatory-compliance)는 서로 다른 규정 준수 표준과 관련된 **규정 준수 도메인** 및 **보안 제어** 에 대해 _기본 제공_ 으로 알려진 Microsoft 생성 및 관리형 이니셔티브 정의를 제공합니다. 이 페이지에는 Azure Data Explorer에 대한 **규정 준수 도메인** 및 **보안 제어** 가 나열되어 있습니다. **보안 제어** 에 대한 기본 제공 기능을 개별적으로 할당하여 Azure 리소스가 특정 표준을 준수하도록 할 수 있습니다.

Azure Portal의 정책 정의에 대한 각 기본 제공 정책 정의 링크의 이름입니다. **Policy Version** 열의 링크를 사용하여 [Azure Policy GitHub 리포지토리](https://github.com/Azure/azure-policy)에서 원본을 봅니다.

> [!IMPORTANT]
> 아래의 각 컨트롤은 하나 이상의 [Azure Policy](/azure/governance/policy/overview) 정의와 연결되어 있습니다. 이러한 정책은 컨트롤을 사용한 [규정 준수 평가](/azure/governance/policy/how-to/get-compliance-data)에 도움이 될 수 있지만, 컨트롤과 하나 이상의 정책 간에 일대일 또는 완벽한 일치 관계가 없는 경우도 많습니다. 따라서 Azure Policy의 **규정 준수** 는 정책 자체만 가리킬 뿐, 컨트롤의 모든 요구 사항을 완벽하게 준수한다는 것은 아닙니다. 또한 규정 준수 표준에는 현재 Azure Policy 정의에서 처리되지 않은 컨트롤이 포함되어 있습니다. 따라서 Azure Policy의 규정 준수는 전반적인 규정 준수 상태를 부분적으로 표시할 뿐입니다. 이 규정 준수 표준에 대한 컨트롤과 Azure Policy 규정 준수 정의 간의 연결은 시간이 지나면 변경될 수 있습니다.

## <a name="cmmc-level-3"></a>CMMC Level 3

모든 Azure 서비스에 사용 가능한 Azure Policy 기본 제공이 이 규정 준수 표준에 매핑되는 방식을 검토하려면 [Azure Policy 규정 준수 - CMMC Level 3](/azure/governance/policy/samples/cmmc-l3)을 참조하세요. 이 규정 준수 표준에 대한 자세한 내용은 [CMMC(사이버 보안 완성 모델 인증)](https://www.acq.osd.mil/cmmc/docs/CMMC_Model_Main_20200203.pdf)를 참조하세요.

|도메인 |컨트롤 ID |컨트롤 제목 |정책<br /><sub>(Azure Portal)</sub> |정책 버전<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|시스템 및 통신 보호 |SC.3.177 |CUI의 기밀성을 보호하기 위해 사용되는 경우 FIPS 인증 암호화를 사용합니다. |[Azure Data Explorer 저장 데이터 암호화는 고객 관리형 키를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F81e74cea-30fd-40d5-802f-d72103c2aaaa) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Data%20Explorer/ADX_CMK.json) |
|시스템 및 통신 보호 |SC.3.177 |CUI의 기밀성을 보호하기 위해 사용되는 경우 FIPS 인증 암호화를 사용합니다. |[Azure Data Explorer에서 디스크 암호화를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff4b53539-8df9-40e4-86c6-6b607703bd4e) |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Data%20Explorer/ADX_disk_encrypted.json) |
|시스템 및 통신 보호 |SC.3.177 |CUI의 기밀성을 보호하기 위해 사용되는 경우 FIPS 인증 암호화를 사용합니다. |[Azure Data Explorer에서 이중 암호화를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fec068d99-e9c7-401f-8cef-5bdde4e6ccf1) |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Data%20Explorer/ADX_doubleEncryption.json) |
|시스템 및 통신 보호 |SC.3.191 |휴지 상태의 CUI 비밀을 보호합니다. |[Azure Data Explorer에서 디스크 암호화를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff4b53539-8df9-40e4-86c6-6b607703bd4e) |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Data%20Explorer/ADX_disk_encrypted.json) |
|시스템 및 통신 보호 |SC.3.191 |휴지 상태의 CUI 비밀을 보호합니다. |[Azure Data Explorer에서 이중 암호화를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fec068d99-e9c7-401f-8cef-5bdde4e6ccf1) |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Data%20Explorer/ADX_doubleEncryption.json) |

## <a name="next-steps"></a>다음 단계

- [Azure Policy 규정 준수](/azure/governance/policy/concepts/regulatory-compliance)에 대해 자세히 알아봅니다.
- [Azure Policy GitHub 리포지토리](https://github.com/Azure/azure-policy)의 기본 제공 기능을 참조하세요.
