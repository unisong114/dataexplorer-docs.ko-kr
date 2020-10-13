---
title: 샌드박스 정책-Azure 데이터 탐색기 | Microsoft Docs
description: Azure 데이터 탐색기의 sandbox 정책 명령에 대해 알아봅니다. Sandbox 정책을 표시, 조정 및 삭제 하는 방법을 참조 하세요.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: ba37147db87c436aff7d77790641b17576e86392
ms.sourcegitcommit: 3d9b4c3c0a2d44834ce4de3c2ae8eb5aa929c40f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/13/2020
ms.locfileid: "92002967"
---
# <a name="sandbox-policy-command"></a>sandbox 정책 명령

다음 명령을 [사용 하 여](../concepts/sandboxes.md) Kusto engine 서비스에서 샌드박스 및 [sandbox 정책을](sandboxpolicy.md) 관리할 수 있습니다.

명령에는 [AllDatabasesAdmin](access-control/role-based-authorization.md) 권한이 필요 합니다.

## <a name="sandbox-policy"></a>샌드박스 정책

### <a name="show-cluster-policy-sandbox"></a>. 클러스터 정책 샌드박스 표시

클러스터 수준에서 구성 된 모든 샌드박스 정책을 표시 합니다.

```kusto
.show cluster policy sandbox
```

### <a name="alter-cluster-policy-sandbox"></a>. 클러스터 정책 sandbox 변경

클러스터 수준에서 샌드박스 정책의 컬렉션을 수정 합니다.

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

### <a name="drop-cluster-policy-sandbox"></a>. 클러스터 정책 샌드박스 삭제

**모든** 샌드박스 정책을 삭제 하려면 다음 명령을 사용 합니다.

```kusto
.delete cluster policy sandbox
```
