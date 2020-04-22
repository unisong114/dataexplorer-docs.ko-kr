---
title: 클러스터 간 조인 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기에서 클러스터 간 조인에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 1199b148fa295ac17417bbf590a73bfc9400a710
ms.sourcegitcommit: 01eb9aaf1df2ebd5002eb7ea7367a9ef85dc4f5d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81765943"
---
# <a name="cross-cluster-join"></a>클러스터 간 조인

::: zone pivot="azuredataexplorer"

클러스터 간 쿼리에 대한 일반적인 설명은 [클러스터 간 또는 데이터베이스 간 쿼리를](cross-cluster-or-database-queries.md) 참조하십시오.

다른 클러스터에 있는 데이터 집합에서 조인 작업을 수행할 수 있습니다. 예를 들면 다음과 같습니다. 

```kusto
T | ... | join (cluster("SomeCluster").database("SomeDB").T2 | ...) on Col1 // (1)

cluster("SomeCluster").database("SomeDB").T | ... | join (cluster("SomeCluster2").database("SomeDB2").T2 | ...) on Col1 // (2)
```

위의 예에서 조인 작업은 현재 클러스터가 "SomeCluster" 또는 "SomeCluster2"가 없다고 가정하는 교차 클러스터 조인입니다.

다음 예제에서는

```kusto
cluster("SomeCluster").database("SomeDB").T | ... | join (cluster("SomeCluster").database("SomeDB2").T2 | ...) on Col1 
```

조인 작업은 두 피연산자의 동일한 클러스터에서 시작되므로 클러스터 간 조인이 아닙니다.

Kusto가 클러스터 간 조인을 만나면 조인 작업 자체를 실행할 위치를 자동으로 결정합니다. 이 결정은 세 가지 가능한 결과 중 하나를 가질 수 있습니다.
* 왼쪽 나산의 클러스터에서 조인 작업을 실행하면 오른쪽 이산도가 먼저 이 클러스터에 의해 페치됩니다. (예제 **(1)에** 가입하면 로컬 클러스터에서 실행됩니다.
* 오른쪽 나연의 클러스터에서 조인 작업을 실행, 왼쪽 된 나체는 먼저이 클러스터에 의해 인출 됩니다. (예제 **(2)에** 가입하면 "SomeCluster2"에서 실행됩니다.
* 로컬로 조인 작업을 실행(쿼리를 받은 클러스터의 의미) 두 개의 진파가 로컬 클러스터에서 먼저 가져옵니다.

실제 결정은 특정 쿼리에 따라 다르며 자동 조인 원격 전략은 (단순화 된 버전) : "피연산자 중 하나가 로컬인 경우 조인은 로컬로 실행됩니다. 두 피연산자 모두 원격 조인이 면 오른쪽 피연산자의 클러스터에서 실행됩니다."

자동 원격 전략을 따르지 않으면 쿼리 성능이 크게 향상될 수 있습니다. 일반적으로 가장 큰 나연산자의 클러스터에서 조인 작업을 실행하는 것이 가장 좋습니다(성능 관점에서).

예를 들어 **(1)** 생성된 데이터 ```T | ...``` 집합이 "SomeCluster"에서 ```cluster("SomeCluster").database("SomeDB").T2 | ...``` 조인 작업을 실행하는 것이 더 효율적입니다.

이는 Kusto가 리모트 힌트를 결합하도록 하여 달성할 수 있습니다. 구문은 다음과 같습니다.

```kusto
T | ... | join hint.remote=<strategy> (cluster("SomeCluster").database("SomeDB").T2 | ...) on Col1
```

다음은 법적 가치에 대한 것입니다.*`strategy`*
* **`left`**- 왼쪽 진연의 클러스터에 조인을 실행 
* **`right`**- 오른쪽 진연의 클러스터에 조인을 실행
* **`local`**- 현재 클러스터의 클러스터에서 조인 실행
* **`auto`**- (기본값) 쿠스토가 자동 원격 결정을 내릴 수 있도록

**참고:** 조인 원격 힌트는 힌트 전략이 조인 작업에 적용되지 않는 경우 Kusto에서 무시됩니다.

::: zone-end

::: zone pivot="azuremonitor"

Azure 모니터에서는 이 성능이 지원되지 않습니다.

::: zone-end
