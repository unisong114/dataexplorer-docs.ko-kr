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
ms.openlocfilehash: 0a59e25c6c38d3189330299af1b19f89357cb456
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81520063"
---
# <a name="sandbox-policy"></a>샌드박스 정책

## <a name="overview"></a>개요

Kusto는 [샌드박스](../concepts/sandboxes.md)내에서 특정 플러그인을 실행하는 것을 지원하며, 샌드박스에서 사용할 수 있는 리소스가 보안 목적뿐만 아니라 리소스 거버넌스 목적으로 제한되고 제어됩니다.

샌드박스는 Kusto 엔진 서비스의 노드에서 실행되며, 이러한 제한 사항 중 일부는 샌드박스 정책에 정의되어 있으며, 각 샌드박스 종류는 자체 샌드박스 정책을 가질 수 있습니다.

샌드박스 정책은 클러스터 수준에서 관리되며 클러스터의 모든 노드에 영향을 미칩니다.

정책을 변경하려면 [AllDatabasesAdmin](../management/access-control/role-based-authorization.md) 권한이 필요합니다.

## <a name="the-policy-object"></a>정책 개체

샌드박스 정책에는 다음과 같은 속성이 있습니다.

* **샌드 박스Kind**: 샌드 박스의 종류를 정의합니다 (예를 `PythonExecution` `RExecution`들어, ).
* **IsEnabled**: 이러한 종류의 샌드박스가 클러스터의 노드에서 실행되도록 활성화되어 있는지 여부를 정의합니다.
* **TargetCountPerNode**: 이러한 종류의 샌드박스 수를 클러스터의 노드에서 실행할 수 있는지 정의합니다.
  * 값은 노드당 프로세서 수의 1에서 두 배 사이일 수 있습니다.
  * 기본값은 `16`입니다.
* **MaxCpuRatePerSandbox**: 단일 샌드박스에서 사용할 수 있는 사용 가능한 모든 코어의 백분율로 최대 CPU 속도를 정의합니다.
  * 값은 1에서 100 사이일 수 있습니다.
  * 기본값은 `50`입니다.
* **MaxMemoryMbPerSandbox**: 단일 샌드박스에서 사용할 수 있는 최대 메모리 양(메가바이트)을 정의합니다.
  * 값은 200에서 65536(64GB) 사이일 수 있습니다.
  * 기본값은 `20480` (20GB)입니다.

## <a name="example"></a>예제

다음 정책은 2가지 종류의 샌드박스에 대해 서로 `PythonExecution` 다른 `RExecution`제한을 설정합니다.

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

## <a name="notes"></a>메모

* 샌드박스 정책의 변경 사항은 변경 이 적용되는 시점부터 생성된 sanbox에 적용됩니다.
  * 정책 변경 이전에 미리 할당된 샌드박스는 쿼리의 일부로 사용될 때까지 이전 정책 제한에 따라 계속 실행됩니다.
* 클러스터 노드가 주기적으로 정책 변경 에 대해 폴링하므로 정책 변경이 적용될 때까지 최대 5분이 지연될 수 있습니다.

## <a name="next-steps"></a>다음 단계

[샌드박스 정책 제어 명령을](../management/sandbox-policy.md) 사용하여 클러스터의 샌드박스 정책을 관리합니다.