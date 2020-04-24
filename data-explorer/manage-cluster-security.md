---
title: Azure 데이터 탐색기에서 클러스터 보호
description: 이 문서에서는 Azure 포털 내의 Azure 데이터 탐색기에서 클러스터를 보호하는 방법을 설명합니다.
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/20/2019
ms.openlocfilehash: 0f935999b68a7283c032d43c42d688b273d5c450
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81496200"
---
# <a name="secure-your-cluster-in-azure-data-explorer---azure-portal"></a>Azure 데이터 탐색기 - Azure 포털에서 클러스터 보호

[Azure 디스크 암호화를](/azure/security/azure-security-disk-encryption-overview) 사용하면 조직 보안 및 규정 준수 약정을 충족하기 위해 데이터를 보호하고 보호할 수 있습니다. 클러스터 가상 시스템의 OS 및 데이터 디스크에 대한 볼륨 암호화를 제공합니다. 또한 [Azure Key Vault와](/azure/key-vault/)통합되어 디스크 암호화 키 및 비밀을 제어 및 관리하고 VM 디스크의 모든 데이터가 암호화되도록 할 수 있습니다. 
  
## <a name="enable-encryption-at-rest-in-the-azure-portal"></a>Azure 포털에서 미사용 암호화 사용
  
클러스터 보안 설정을 사용하면 클러스터에서 디스크 암호화를 활성화할 수 있습니다. 클러스터에서 [미사용 암호화를](/azure/security/fundamentals/encryption-atrest) 사용하도록 설정하면 저장된 데이터(미사용 데이터)에 대한 데이터 보호가 제공됩니다. 

1. Azure 포털에서 Azure 데이터 탐색기 클러스터 리소스로 이동합니다. **설정** 제목 아래에서 **보안**을 선택합니다. 

    ![미사용 암호화 켜기](media/manage-cluster-security/security-encryption-at-rest.png)

1. **보안** 창에서 **디스크 암호화** 보안 설정에 대해 **켜기(On)를** 선택합니다. 

1. **저장**을 선택합니다.
 
> [!NOTE]
> 암호화를 활성화한 후 **해제를** 선택합니다.

## <a name="next-steps"></a>다음 단계

[클러스터 상태 확인](/azure/data-explorer/check-cluster-health)
