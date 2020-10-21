---
title: 클러스터 간 조인-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 클러스터 간 조인에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: a7c8f89886a8c12941dbc218ad69b35eebd7f1c7
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92246595"
---
# <a name="cross-cluster-join"></a>클러스터 간 조인

::: zone pivot="azuredataexplorer"

클러스터 간 쿼리에 대 한 일반적인 설명은 [클러스터 간 또는 데이터베이스 간 쿼리](cross-cluster-or-database-queries.md) 를 참조 하세요.

다른 클러스터에 있는 데이터 집합에 대 한 조인 작업을 수행할 수 있습니다. 예를 들면 다음과 같습니다.

```kusto
T | ... | join (cluster("SomeCluster").database("SomeDB").T2 | ...) on Col1 // (1)

cluster("SomeCluster").database("SomeDB").T | ... | join (cluster("SomeCluster2").database("SomeDB2").T2 | ...) on Col1 // (2)
```

위의 예제에서 조인 작업은 현재 클러스터가 "SomeCluster" 또는 "SomeCluster2"가 아님을 가정 하 고 클러스터 간 조인입니다.

다음 예제에서,

```kusto
cluster("SomeCluster").database("SomeDB").T | ... | join (cluster("SomeCluster").database("SomeDB2").T2 | ...) on Col1 
```

조인 작업은 두 피연산자가 동일한 클러스터에서 시작 되므로 클러스터 간 조인이 아닙니다.

Kusto가 클러스터 간 조인을 발견 하면 자동으로 조인 작업을 실행할 위치를 결정 합니다. 이 결정은 다음과 같은 세 가지 결과 중 하나가 발생할 수 있습니다.

* 왼쪽 피연산자의 클러스터에서 조인 작업을 실행 합니다 .이 클러스터는 오른쪽 피연산자를 먼저 인출 합니다. 예 ( **1)** 는 로컬 클러스터에서 실행 됩니다.
* 오른쪽 피연산자의 클러스터에서 조인 작업을 실행 합니다 .이 클러스터는 왼쪽 피연산자를 먼저 인출 합니다. (예: **2)** 는 "SomeCluster2"에서 실행 됩니다.
* Join 작업을 로컬로 실행 (쿼리를 받은 클러스터에서 의미) 하는 경우 로컬 클러스터에서 두 피연산자를 먼저 인출 합니다.

실제 의사 결정은 특정 쿼리에 따라 달라 집니다. 자동 조인 원격 전략은 (단순화 된 버전)입니다. "피연산자 중 하나가 로컬인 경우 조인이 로컬에서 실행 됩니다. 두 피연산자가 모두 원격 이면 오른쪽 피연산자의 클러스터에서 조인이 실행 됩니다.

경우에 따라 자동 원격 전략을 따르지 않는 경우 쿼리 성능이 향상 될 수 있습니다. 이 경우 가장 큰 피연산자의 클러스터에서 조인 작업을 실행 합니다.

예 **(1)** 로 생성 된 데이터 집합이에 `T | ...` 의해 생성 된 데이터 집합 보다 훨씬 작은 경우에는 `cluster("SomeCluster").database("SomeDB").T2 | ...` "SomeCluster"에서 조인 작업을 실행 하는 것이 더 효율적입니다.

이 작업은 Kusto join 원격 힌트를 제공 하 여 수행할 수 있습니다. 구문은 다음과 같습니다.

```kusto
T | ... | join hint.remote=<strategy> (cluster("SomeCluster").database("SomeDB").T2 | ...) on Col1
```

다음은에 대 한 올바른 값입니다. `strategy`
* `left` -왼쪽 피연산자의 클러스터에서 join을 실행 합니다. 
* `right` -오른쪽 피연산자의 클러스터에서 join을 실행 합니다.
* `local` -현재 클러스터의 클러스터에서 join을 실행 합니다.
* `auto` -(기본값) 자동 원격 결정을 수행 하도록 Kusto

> [!Note]
> 힌트 전략이 조인 작업에 적용 되지 않는 경우에는 Kusto에서 조인 원격 힌트가 무시 됩니다.

::: zone-end

::: zone pivot="azuremonitor"

이 기능은에서 지원 되지 않습니다 Azure Monitor

::: zone-end
