---
title: 용량 정책-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 용량 정책을 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/12/2020
ms.openlocfilehash: 0ecdee4171cb6deaa4cc6c233b8951c4eb54953c
ms.sourcegitcommit: 9810acae3f1c83b8efe7b952d3bada2ff496b024
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/18/2020
ms.locfileid: "83444701"
---
# <a name="capacity-policy"></a>용량 정책

용량 정책은 클러스터의 데이터 관리 작업에 사용 되는 계산 리소스를 제어 하는 데 사용 됩니다.

## <a name="the-capacity-policy-object"></a>용량 정책 개체

용량 정책은 다음으로 구성 됩니다.

* [IngestionCapacity](#ingestion-capacity)
* [ExtentsMergeCapacity](#extents-merge-capacity)
* [ExtentsPurgeRebuildCapacity](#extents-purge-rebuild-capacity)
* [ExportCapacity](#export-capacity)
* [ExtentsPartitionCapacity](#extents-partition-capacity)

## <a name="ingestion-capacity"></a>수집 용량

|속성                           |유형    |설명                                                                                                                                                                               |
|-----------------------------------|--------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|ClusterMaximumConcurrentOperations |long    |클러스터의 동시 수집 작업 수에 대 한 최대 값                                                                                                            |
|CoreUtilizationCoefficient         |double  |수집 용량을 계산할 때 사용할 코어 비율의 계수 (계산 결과는 항상에 의해 정규화 됨 `ClusterMaximumConcurrentOperations` ) |                                                                                                                             |

클러스터의 총 수집 용량 ( [. show capacity](../management/diagnostics.md#show-capacity))은 다음과 같이 계산 됩니다.

최소 ( `ClusterMaximumConcurrentOperations` , `Number of nodes in cluster` * 최대 (1, `Core count per node`  *  `CoreUtilizationCoefficient` ))

> [!Note]
> 노드가 세 개 이상인 클러스터에서는 관리 노드가 수집 작업 수행에 참여 하지 않습니다. 는 `Number of nodes in cluster` 1로 줄어듭니다.

## <a name="extents-merge-capacity"></a>익스텐트 병합 용량

|속성                           |유형    |설명                                                                                    |
|-----------------------------------|--------|-----------------------------------------------------------------------------------------------|
|MaximumConcurrentOperationsPerNode |long    |단일 노드의 동시 익스텐트 병합/다시 작성 작업 수에 대 한 최대 값 |

클러스터의 총 익스텐트 병합 용량 ( [. 용량 표시](../management/diagnostics.md#show-capacity)와 같이)은 다음을 통해 계산 됩니다.

`Number of nodes in cluster`.x`MaximumConcurrentOperationsPerNode`

> [!Note]
> * `MaximumConcurrentOperationsPerNode`는 더 높은 값으로 설정 되지 않은 경우 시스템에 의해 자동으로 조정 됩니다 (1, 5).
> * 노드가 세 개 이상인 클러스터에서는 관리 노드가 병합 작업 수행에 참여 하지 않습니다. 는 `Number of nodes in cluster` 1로 줄어듭니다.

## <a name="extents-purge-rebuild-capacity"></a>익스텐트 재작성 용량 제거

|속성                           |유형    |설명                                                                                                                           |
|-----------------------------------|--------|--------------------------------------------------------------------------------------------------------------------------------------|
|MaximumConcurrentOperationsPerNode |long    |단일 노드에서의 제거 작업에 대 한 동시 다시 빌드 익스텐트의 최대 값 |

클러스터의 총 익스텐트 재작성 용량 제거 ( [. 용량 표시](../management/diagnostics.md#show-capacity)와 같이)는 다음을 통해 계산 됩니다.

`Number of nodes in cluster`.x`MaximumConcurrentOperationsPerNode`

> [!Note]
> 노드가 세 개 이상인 클러스터에서는 관리 노드가 병합 작업 수행에 참여 하지 않습니다. 는 `Number of nodes in cluster` 1로 줄어듭니다.

## <a name="export-capacity"></a>용량 내보내기

|속성                           |유형    |설명                                                                                                                                                                            |
|-----------------------------------|--------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|ClusterMaximumConcurrentOperations |long    |클러스터의 동시 내보내기 작업 수에 대 한 최대값입니다.                                                                                                           |
|CoreUtilizationCoefficient         |double  |내보내기 용량을 계산할 때 사용할 코어 비율의 계수입니다. 계산 결과는 항상으로 정규화 됩니다 `ClusterMaximumConcurrentOperations` . |

클러스터의 총 내보내기 용량 ( [. 용량 표시](../management/diagnostics.md#show-capacity)와 같이)은 다음을 통해 계산 됩니다.

최소 ( `ClusterMaximumConcurrentOperations` , `Number of nodes in cluster` * 최대 (1, `Core count per node`  *  `CoreUtilizationCoefficient` ))

> [!Note]
> 노드가 세 개 이상인 클러스터에서 관리 노드는 내보내기 작업을 수행 하는 작업에 참여 하지 않습니다. 는 `Number of nodes in cluster` 1로 줄어듭니다.

## <a name="extents-partition-capacity"></a>익스텐트 파티션 용량

|속성                           |유형    |설명                                                                             |
|-----------------------------------|--------|----------------------------------------------------------------------------------------|
|ClusterMaximumConcurrentOperations |long    |클러스터의 동시 익스텐트 파티션 작업 수에 대 한 최대값입니다. |

클러스터의 총 익스텐트 파티션 용량 ( [. show capacity](../management/diagnostics.md#show-capacity))은 단일 속성인로 정의 `ClusterMaximumConcurrentOperations` 됩니다.

> [!Note]
> `ClusterMaximumConcurrentOperations`는 더 높은 값으로 설정 되지 않은 경우 시스템에 의해 [1, 16] 범위 내에서 자동으로 조정 됩니다.

## <a name="defaults"></a>기본값

기본 용량 정책의 JSON 표현은 다음과 같습니다.

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

## <a name="control-commands"></a>제어 명령

> [!WARNING]
> 클러스터의 사용 가능한 리소스에 대 한 잠재적인 영향 때문에 용량 정책을 변경 하는 것은 거의 권장 되지 않습니다.

* 클러스터 [정책 용량 표시](capacity-policy.md#show-cluster-policy-capacity) 를 사용 하 여 클러스터의 현재 용량 정책을 표시 합니다.

* 클러스터 [정책 용량 변경](capacity-policy.md#alter-cluster-policy-capacity) 을 사용 하 여 클러스터의 용량 정책을 변경 합니다.

## <a name="throttling"></a>제한

Kusto는 다음과 같은 사용자 시작 명령에 대 한 동시 요청 수를 제한 합니다.

* Ingestions ( [여기](../management/data-ingestion/index.md)에 나열 된 모든 명령이 포함 되어 있습니다.)
   * 제한은 [용량 정책](#capacity-policy)에 정의 된 대로입니다.
* 제거
   * 전역은 현재 클러스터당 하나의에 고정 되어 있습니다.
   * 제거 작업을 수행 하는 동안 동시 다시 작성 작업의 수를 확인 하기 위해 제거 다시 작성 용량을 내부적으로 사용 합니다. 제거 명령은이 프로세스로 인해 차단/제한 되지 않지만 다시 작성 용량 제거에 따라 더 빠르거나 더 느리게 작동 합니다.
* 내보내기
   * 제한은 [용량 정책](#capacity-policy)에 정의 된 대로입니다.

클러스터가 허용 된 동시 작업을 초과 하는 작업을 감지 하면 429 ("제한 된") HTTP 코드로 응답 합니다.
백오프 후 작업을 다시 시도 하세요.
