---
title: 샌드박스 정책-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 Sandbox 정책에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: 786f771878a7216b62dce127391f0e7967e954f6
ms.sourcegitcommit: 188f89553b9d0230a8e7152fa1fce56c09ebb6d6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/08/2020
ms.locfileid: "84512456"
---
# <a name="sandbox-policy"></a>샌드박스 정책

Azure 데이터 탐색기는 보안 및 리소스 관리를 위해 사용 가능한 리소스를 제한 하 고 제어 하는 [샌드박스](../concepts/sandboxes.md) 내의 특정 플러그 인을 실행 합니다.

샌드박스는 Kusto engine의 노드에서 실행 됩니다. 이러한 제한 사항 중 일부는 샌드박스 정책에 정의 되어 있습니다. 여기에는 각 샌드박스 유형에 고유한 정책이 포함 될 수 있습니다.

Sandbox 정책은 클러스터 수준에서 관리 되며 클러스터의 모든 노드에 영향을 줍니다.

정책을 변경 하려면 [AllDatabasesAdmin](../management/access-control/role-based-authorization.md) 권한이 필요 합니다.

## <a name="the-policy-object"></a>정책 개체

Sandbox 정책에는 다음과 같은 속성이 있습니다.

* **SandboxKind**: 샌드박스 유형 (예:,,)을 정의 `PythonExecution` `RExecution` 합니다.
* **IsEnabled**:이 형식의 샌드박스에서 클러스터의 노드에서 실행 될 수 있는지 여부를 정의 합니다.
* **Targetcountpernode**: 클러스터의 노드에서 실행 될 수 있는이 유형의 샌드박스 수를 정의 합니다.
  * 값은 노드당 프로세서 수의 1 ~ 2 배가 될 수 있습니다.
  * 기본값은 16입니다.
* **MaxCpuRatePerSandbox**: 단일 샌드박스에서 사용할 수 있는 모든 사용 가능한 코어의 백분율로 최대 CPU 비율을 정의 합니다.
  * 값은 1에서 100 사이가 될 수 있습니다.
  * 기본값은 50입니다.
* **MaxMemoryMbPerSandbox**: 단일 샌드박스에서 사용할 수 있는 최대 메모리 양 (mb)을 정의 합니다.
  * 값은 200에서 65536 (64GB) 사이가 될 수 있습니다.
  * 기본값은 20480 (20GB)입니다.

## <a name="example"></a>예제

다음 정책은 및 샌드박스에 대해 서로 다른 제한을 설정 합니다 `PythonExecution` `RExecution` .

```json
[
  {
    "SandboxKind": "PythonExecution",
    "IsEnabled": true,
    "TargetCountPerNode": 4,
    "MaxCpuRatePerSandbox": 55,
    "MaxMemoryMbPerSandbox": 65536
  },
  {
    "SandboxKind": "RExecution",
    "IsEnabled": true,
    "TargetCountPerNode": 2,
    "MaxCpuRatePerSandbox": 50,
    "MaxMemoryMbPerSandbox": 10240
  }
]
```

> [!NOTE]
> * 샌드박스 정책에 대 한 변경 내용은 변경이 적용 되는 시점부터 생성 된 샌드박스에 적용 됩니다. 정책 변경 전에 사전 할당 된 샌드박스는 쿼리의 일부로 사용 될 때까지 이전 정책 제한에 따라 계속 실행 됩니다.
> * 클러스터 노드가 정책 변경을 정기적으로 폴링하여 정책 변경이 적용 될 때까지 최대 5 분까지 지연 될 수 있습니다.

## <a name="next-steps"></a>다음 단계

[샌드박스 정책 제어 명령을](../management/sandbox-policy.md) 사용 하 여 클러스터의 샌드박스 정책을 관리 합니다.
 
