---
title: 샌드박스 정책-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 Sandbox 정책에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: 7d56d602f53db29f5ea558acb0e9e4288e5ac6e3
ms.sourcegitcommit: b08b1546122b64fb8e465073c93c78c7943824d9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/06/2020
ms.locfileid: "85967488"
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

