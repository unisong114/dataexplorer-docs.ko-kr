---
title: 샌드박스 정책 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 샌드박스 정책에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: 7ac5a92b2084eaf2b447f296be34b2f4e79e1bb7
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81520131"
---
# <a name="sandbox-policy"></a>샌드박스 정책

다음 명령을 사용하면 Kusto 엔진 서비스에서 [샌드박스](../concepts/sandboxes.md) 및 [샌드박스 정책을](sandboxpolicy.md) 관리할 수 있습니다.

명령에는 [AllDatabasesAdmin](access-control/role-based-authorization.md) 권한이 필요합니다.

## <a name="sandbox-policy"></a>샌드박스 정책

### <a name="show-cluster-policy-sandbox"></a>.show 클러스터 정책 샌드박스

클러스터 수준에서 구성된 모든 샌드박스 정책을 표시합니다.

```kusto
.show cluster policy sandbox
```

### <a name="alter-cluster-policy-sandbox"></a>클러스터 정책 샌드박스 변경

클러스터 수준에서 샌드박스 정책 컬렉션을 수정합니다.

```kusto
.alter cluster policy sandbox @'['
  '{'
    '"SandboxKind": "PythonExecution",'
    '"IsEnabled": true,'
    '"TargetCountPerNode": 4,'
    '"MaxCpuRatePerSandbox": 50,'
    '"MaxMemoryMbPerSandbox": 10240'
  '},'
  '{'
    '"SandboxKind": "RExecution",'
    '"IsEnabled": true,'
    '"TargetCountPerNode": 4,'
    '"MaxCpuRatePerSandbox": 50,'
    '"MaxMemoryMbPerSandbox": 10240'
  '}'
']'
```

### <a name="drop-cluster-policy-sandbox"></a>.drop 클러스터 정책 샌드박스

**모든** 샌드박스 정책을 삭제하는 경우 다음 명령을 사용합니다.

```kusto
.delete cluster policy sandbox
```

