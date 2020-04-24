---
title: 용량 정책 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 용량 정책에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/12/2020
ms.openlocfilehash: af648bd0a4b328477b14e20a2457e3e914df2827
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81522001"
---
# <a name="capacity-policy"></a>용량 정책

용량 정책은 데이터 수집 및 기타 데이터 정리 작업(예: 익스텐트 병합)을 수행하는 데 사용되는 컴퓨팅 리소스를 제어하는 데 사용됩니다.

## <a name="the-capacity-policy-object"></a>용량 정책 개체

용량 정책은 로 `IngestionCapacity` `ExtentsMergeCapacity`구성됩니다. `ExtentsPurgeRebuildCapacity` `ExportCapacity`

### <a name="ingestion-capacity"></a>섭취 능력

|속성                           |Type    |Description                                                                                                                                                                               |
|-----------------------------------|--------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|클러스터최대동시연운영 |long    |클러스터의 동시 흡기 작업 수에 대한 최대 값                                                                                                            |
|코어활용코효율         |double  |섭취 용량을 계산할 때 사용할 코어 백분율계수(계산 결과는 항상 `ClusterMaximumConcurrentOperations`정규화됩니다). |                                                                                                                             |

클러스터의 총 [수용량(.show 용량으로](../management/diagnostics.md#show-capacity)표시됨)은 다음과 같이 계산됩니다.

`ClusterMaximumConcurrentOperations`최소(, `Number of nodes in cluster` * 최대값)(1, `Core count per node`  *  `CoreUtilizationCoefficient`))

> [!Note] 
> 노드가 세 개 이상인 클러스터에서는 관리자 노드가 섭취 작업에 `Number of nodes in cluster` 참여하지 않으므로 1씩 줄어듭니다.

### <a name="extents-merge-capacity"></a>용량 병합 범위

|속성                           |Type    |Description                                                                                    |
|-----------------------------------|--------|-----------------------------------------------------------------------------------------------|
|최대 동시 전류운영퍼노드 |long    |단일 노드에서 동시 익스텐트 병합/재생성 작업 수에 대한 최대 값 |

클러스터의 총 익스텐트 병합 [용량(.show 용량으로](../management/diagnostics.md#show-capacity)표시됨)은 다음과 같이 계산됩니다.

`Number of nodes in cluster`Ⅹ`MaximumConcurrentOperationsPerNode`

> [!Note] 
> 노드가 세 개 이상인 클러스터에서는 관리자 노드가 병합 작업을 `Number of nodes in cluster` 수행하는 데 참여하지 않으므로 1씩 줄어듭니다.

### <a name="extents-purge-rebuild-capacity"></a>익스텐트 제거 재구축 용량

|속성                           |Type    |Description                                                                                                                           |
|-----------------------------------|--------|--------------------------------------------------------------------------------------------------------------------------------------|
|최대 동시 전류운영퍼노드 |long    |단일 노드에서 동시 익스텐트 제거 재구축 작업(제거 작업에 대한 빌드 익스텐트)에 대한 최대 값 |

클러스터의 총 익스텐션은 [.show 용량으로](../management/diagnostics.md#show-capacity)표시된 대로 재생성 용량을 제거합니다.

`Number of nodes in cluster`Ⅹ`MaximumConcurrentOperationsPerNode`

> [!Note] 
> 노드가 세 개 이상인 클러스터에서는 관리자 노드가 병합 작업을 `Number of nodes in cluster` 수행하는 데 참여하지 않으므로 1씩 줄어듭니다.

### <a name="export-capacity"></a>내보내기 용량

|속성                           |Type    |Description                                                                                                                                                                            |
|-----------------------------------|--------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|클러스터최대동시연운영 |long    |클러스터의 동시 내보내기 작업 수에 대한 최대 값입니다.                                                                                                           |
|코어활용코효율         |double  |내보내기 용량을 계산할 때 사용할 코어 백분율계수(계산 결과는 항상 정규화됩니다). `ClusterMaximumConcurrentOperations` |

클러스터의 총 내보내기 [용량(.show 용량으로](../management/diagnostics.md#show-capacity)표시된 대로)은 다음과 같이 계산됩니다.

`ClusterMaximumConcurrentOperations`최소(, `Number of nodes in cluster` * 최대값)(1, `Core count per node`  *  `CoreUtilizationCoefficient`))

> [!Note] 
> 노드가 세 개 이상인 클러스터에서는 관리자 노드가 내보내기 작업에 `Number of nodes in cluster` 참여하지 않으므로 1씩 줄어듭니다.

### <a name="extents-partition-capacity"></a>파티션 용량 범위

|속성                           |Type    |Description                                                                             |
|-----------------------------------|--------|----------------------------------------------------------------------------------------|
|클러스터최대동시연운영 |long    |클러스터의 동시 익스텐트 파티션 작업 수에 대한 최대 값입니다. |

클러스터의 총 익스텐션 파티션 용량(.show [용량으로](../management/diagnostics.md#show-capacity)표시됨)은 `ClusterMaximumConcurrentOperations`단일 속성으로 정의됩니다.

### <a name="defaults"></a>기본값

기본 용량 정책에는 다음과 같은 JSON 표현이 있습니다.

```kusto 
{
  "IngestionCapacity": {
    "ClusterMaximumConcurrentOperations": 512,
    "CoreUtilizationCoefficient": 0.75
  },
  "ExtentsMergeCapacity": {
    "MaximumConcurrentOperationsPerNode": 1
  },
  "ExtentsPurgeRebuildCapacity": {
    "MaximumConcurrentOperationsPerNode": 1
  },
  "ExportCapacity": {
    "ClusterMaximumConcurrentOperations": 100,
    "CoreUtilizationCoefficient": 0.25
  }
}
```

> [!WARNING]
> Kusto 팀과 먼저 상의하지 않고 용량 정책을 변경하는 것은 **거의** 권장되지 않습니다.

## <a name="control-commands"></a>제어 명령

* [.show 클러스터 정책 용량을](capacity-policy.md#show-cluster-policy-capacity) 사용하여 클러스터의 현재 용량 정책을 표시합니다.
* [.alter 클러스터 정책 용량을](capacity-policy.md#alter-cluster-policy-capacity) 사용하여 클러스터의 용량 정책을 변경합니다.

## <a name="throttling"></a>제한

Kusto는 다음 명령에 대한 동시 요청 수를 제한합니다.

1. 인스티온(여기에 나열된 모든 명령 [here](../management/data-ingestion/index.md)포함)
      * 제한은 [용량 정책에](#capacity-policy)정의된 대로 입니다.
1. 병합
      * 제한은 [용량 정책에](#capacity-policy)정의된 대로 입니다.
1. 제거
      * 글로벌은 현재 클러스터당 하나씩 고정되어 있습니다.
      * 퍼지 재생성 용량은 내부적으로 사용되어 제거 명령 중에 동시 재생성 작업 수를 결정합니다(제거 명령은 이로 인해 차단/제한되지 않지만 제거 재구축 용량에 따라 더 빠르고 느리게 작동합니다).
1. 수출
      * 제한은 [용량 정책에](#capacity-policy)정의된 대로 입니다.


Kusto가 일부 작업이 허용된 동시 작업을 초과했다는 것을 감지하면 Kusto는 429 HTTP 코드로 응답합니다.
클라이언트는 일부 백오프 후 작업을 다시 시도해야 합니다.