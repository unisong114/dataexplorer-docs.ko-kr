---
title: 용량 정책 제어 명령 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 용량 정책 제어 명령에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/02/2020
ms.openlocfilehash: 929cfa885a7373b400b832d908677a7a5fb93ef6
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81522086"
---
# <a name="capacity-policy-control-commands"></a>용량 정책 제어 명령

다음 제어 명령을 사용하여 클러스터의 [용량 정책을](../management/capacitypolicy.md)관리할 수 있습니다.

명령에는 [AllDatabasesAdmin](../management/access-control/role-based-authorization.md) 권한이 필요합니다.

## <a name="show-cluster-policy-capacity"></a>클러스터 정책 용량 표시

```kusto
.show cluster policy capacity
```

클러스터의 현재 용량 정책을 표시합니다.

**출력**

|정책 이름 | 엔터티 이름 | 정책 | 자식 엔터티 | 엔터티 형식
|---|---|---|---|---
|용량 정책 | | 정책을 나타내는 JSON 형식의 문자열 | 클러스터의 데이터베이스 목록 |클러스터


## <a name="alter-cluster-policy-capacity"></a>클러스터 정책 용량 변경

```kusto
.alter cluster policy capacity @'{ ... capacity policy JSON representation ... }'
.alter-merge cluster policy capacity @'{ ... capacity policy partial-JSON representation ... }'
```

**참고:** 클러스터 용량 정책 변경 사항이 적용되기까지 최대 1시간이 걸릴 수 있습니다.

**예:**

* 클러스터 정책의 모든 속성을 명시적으로 변경합니다.

```kusto
.alter cluster policy capacity
'{'
  '"IngestionCapacity": {'
    '"ClusterMaximumConcurrentOperations": 512,'
    '"CoreUtilizationCoefficient": 0.75'
  '},'
  '"ExtentsMergeCapacity": {'
    '"MaximumConcurrentOperationsPerNode": 1'
  '},'
  '"ExtentsPurgeRebuildCapacity": {'
    '"MaximumConcurrentOperationsPerNode": 1'
  '},'
  '"ExportCapacity": {'
    '"ClusterMaximumConcurrentOperations": 100,'
    '"CoreUtilizationCoefficient": 0.25'
  '},'
  '"ExtentsPartitionCapacity": {'
    '"ClusterMaximumConcurrentOperations": 4'
  '}'
'}'
```

* 클러스터 수준 정책의 단일 속성을 변경하여 다른 모든 속성을 그대로 유지합니다.

```kusto
.alter-merge cluster policy capacity
'{'
  '"ExtentsPartitionCapacity": {'
    '"MaximumConcurrentOperationsPerNode": 4'
  '}'
'}'
```
