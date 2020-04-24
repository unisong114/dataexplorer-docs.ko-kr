---
title: 쿼리 순서 섞기-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 순서 섞기 쿼리를 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 600e561937b779ff9dd10d5d82f5522d204466a0
ms.sourcegitcommit: 2e63c7c668c8a6200f99f18e39c3677fcba01453
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/24/2020
ms.locfileid: "82117695"
---
# <a name="shuffle-query"></a>쿼리 순서 섞기

순서 섞기 쿼리는 실제 데이터에 따라 성능이 크게 향상 될 수 있는 순서 섞기 전략을 지 원하는 연산자 집합에 대 한 의미 체계 유지 변환입니다.

Kusto에서 순서 섞기를 지 원하는 연산자는 [조인](joinoperator.md), [요약](summarizeoperator.md) 및 [시리즈](make-seriesoperator.md)입니다.

쿼리 매개 변수 `hint.strategy = shuffle` 또는 `hint.shufflekey = <key>`에서 순서 섞기 쿼리 전략을 설정할 수 있습니다.

테이블에서 [데이터 분할 정책](../management/partitioningpolicy.md) 정의를 살펴볼 수도 있습니다. 클러스터 노드 간에 이동 `shufflekey` 하는 데 필요한 데이터 양이 크게 줄어도가 테이블의 해시 파티션 키 인 쿼리는 더 잘 수행 될 것으로 예상 됩니다.

**구문**

```kusto
T | where Event=="Start" | project ActivityId, Started=Timestamp
| join hint.strategy = shuffle (T | where Event=="End" | project ActivityId, Ended=Timestamp)
  on ActivityId
| extend Duration=Ended - Started
| summarize avg(Duration)
```

```kusto
T
| summarize hint.strategy = shuffle count(), avg(price) by supplier
```

```kusto
T
| make-series hint.shufflekey = Fruit PriceAvg=avg(Price) default=0  on Purchase from datetime(2016-09-10) to datetime(2016-09-13) step 1d by Supplier, Fruit
```

이 전략은 각 노드가 데이터의 한 파티션을 처리 하는 모든 클러스터 노드의 부하를 공유 합니다.
키 (`join` 키, `summarize` 키 또는 `make-series` 키)가 높은 카디널리티를 사용 하 여 쿼리 제한에 도달 하는 경우에는 무작위 쿼리 전략을 사용 하는 것이 유용 합니다.

**힌트 간 차이입니다. 전략 = 순서 섞기와 shufflekey = key**

`hint.strategy=shuffle`섞은 연산자가 모든 키로 섞은 됨을 의미 합니다.
예를 들어 다음 쿼리에서는

```kusto
T | where Event=="Start" | project ActivityId, Started=Timestamp
| join hint.strategy = shuffle (T | where Event=="End" | project ActivityId, Ended=Timestamp)
  on ActivityId, ProcessId
| extend Duration=Ended - Started
| summarize avg(Duration)
```

데이터를 섞 습니다 하는 해시 함수는 키의 활동 Id와 ProcessId를 모두 사용 합니다.

위의 쿼리는 다음과 같습니다.

```kusto
T | where Event=="Start" | project ActivityId, Started=Timestamp
| join hint.shufflekey = ActivityId hint.shufflekey = ProcessId (T | where Event=="End" | project ActivityId, Ended=Timestamp)
  on ActivityId, ProcessId
| extend Duration=Ended - Started
| summarize avg(Duration)
```

이 힌트는 섞은 연산자의 모든 키로 데이터를 순서 섞기 하는 데 관심이 있을 때 사용할 수 있습니다 .이는 복합 키가 너무 고유 하지만 각 키가 충분히 고유 하지 않기 때문입니다.
섞은 연산자에 또는 `summarize` `join`와 같은 다른 shufflable 연산자가 있는 경우 쿼리는 더 복잡 해지고 힌트를 적용 합니다. 전략 = 순서 섞기는 적용 되지 않습니다.

예를 들어:

```kusto
T
| where Event=="Start"
| project ActivityId, Started=Timestamp, numeric_column
| summarize count(), numeric_column = any(numeric_column) by ActivityId
| join
    hint.strategy = shuffle (T
    | where Event=="End"
    | project ActivityId, Ended=Timestamp, numeric_column
)
on ActivityId, numeric_column
| extend Duration=Ended - Started
| summarize avg(Duration)
```

이 경우 쿼리 계획 중에 전략을 무시 `hint.strategy=shuffle` 하는 대신를 적용 하 여 복합 키 [`ActivityId`, `numeric_column`]를 기준으로 데이터 순서를 적용 하면 결과가 올바르지 않습니다.
`summarize` `join` 인 `ActivityId`키의 하위 집합을 기준으로 `join` groubs의 왼쪽에 있는입니다. 즉, 데이터는 `summarize` 복합 키 [`ActivityId`, `numeric_column`] `ActivityId` 에 의해 분할 되는 동안는 키를 기준으로 그룹화 됩니다.
복합 키 [`ActivityId`, `numeric_column`]로 순서 섞기 하는 것은 키가 아닌 키에 대 한 유효한 순서 섞기 결과가 올바르지 않을 수 있음을 의미 하지 않습니다.

이 예제에서는 복합 키에 사용 되는 해시 함수가 인 것으로 가정 합니다.`binary_xor(hash(key1, 100) , hash(key2, 100))`

```kusto

datatable(ActivityId:string, NumericColumn:long)
[
"activity1", 2,
"activity1" ,1,
]
| extend hash_by_key = binary_xor(hash(ActivityId, 100) , hash(NumericColumn, 100))
```

|ActivityId|NumericColumn|hash_by_key|
|---|---|---|
|activity1|2|56|
|activity1|1|65|



두 레코드에 대 한 복합 키가 서로 다른 파티션 56 및 65에 매핑 되었지만 이러한 `ActivityId` 두 레코드의 값은 동일 합니다. 즉,의 왼쪽 `summarize` `join` 에 있는가 같은 파티션에 있는 유사한 값 `ActivityId` 을 예상 하는 defintely는 잘못 된 결과를 생성 합니다.

이 경우에서는 `hint.shufflekey` 모든 shuffelable 연산자에 대 한 공통 키인 조인 `hint.shufflekey = ActivityId` 에 순서 섞기 키를 지정 하 여이 문제를 해결 합니다.
이 경우 순서 섞기은 안전 하며, 두 가지 모두 `join` 동일한 `summarize` 키로 섞 습니다, 모든 유사한 값은 결과가 올바른 파티션에 defintely 됩니다.

```kusto
T
| where Event=="Start"
| project ActivityId, Started=Timestamp, numeric_column
| summarize count(), numeric_column = any(numeric_column) by ActivityId
| join
    hint.shufflekey = ActivityId (T
    | where Event=="End"
    | project ActivityId, Ended=Timestamp, numeric_column
)
on ActivityId, numeric_column
| extend Duration=Ended - Started
| summarize avg(Duration)
```

|ActivityId|NumericColumn|hash_by_key|
|---|---|---|
|activity1|2|56|
|activity1|1|65|

순서 섞기 쿼리에서 기본 파티션 번호는 클러스터 노드 번호입니다. 이 숫자는 파티션 수를 제어 하는 `hint.num_partitions = total_partitions` 구문을 사용 하 여 재정의할 수 있습니다.

이 힌트는 기본 파티션 번호가 작고 쿼리가 여전히 실패 하거나 실행 시간이 길어질 때 클러스터에 적은 수의 클러스터 노드가 있는 경우에 유용 합니다.

많은 파티션을 설정 하면 성능이 저하 되 고 더 많은 클러스터 리소스가 소비 될 수 있으므로, 파티션 번호를 신중 하 게 선택 하는 것이 좋습니다. 전략 = 순서 섞기 및 파티션을 점차적으로 늘립니다.

**예제**

다음 예에서는 순서 섞기 `summarize` 에서 성능을 크게 향상 시키는 방법을 보여 줍니다.

원본 테이블은 150M 레코드를 포함 하 고 group by key의 카디널리티는 10 개의 클러스터 노드에 분산 된 10M입니다.

정기 `summarize` 전략을 실행 하면 쿼리가 1:08 후에 종료 되 고 메모리 사용량의 최대 사용량은-3gb입니다.

```kusto
orders
| summarize arg_max(o_orderdate, o_totalprice) by o_custkey 
| where o_totalprice < 1000
| count
```

|개수|
|---|
|1086|

순서 섞기 `summarize` 전략을 사용 하는 동안 쿼리는 ~ 7 초 후에 종료 되 고 메모리 사용량의 최대 사용량은 0.43입니다.

```kusto
orders
| summarize hint.strategy = shuffle arg_max(o_orderdate, o_totalprice) by o_custkey 
| where o_totalprice < 1000
| count
```

|개수|
|---|
|1086|

다음 예에서는 2 개의 클러스터 노드가 있는 클러스터의 향상 된 기능을 보여 줍니다 .이 테이블에는 60M 레코드가 있고 group by key의 카디널리티는 2M입니다.

을 (를) `hint.num_partitions` 사용 하지 않고 쿼리를 실행 하면 두 개의 파티션 (클러스터 노드 번호)만 사용 하 고 다음 쿼리는 ~ 1:10 분이 소요 됩니다.

```kusto
lineitem    
| summarize hint.strategy = shuffle dcount(l_comment), dcount(l_shipdate) by l_partkey 
| consume
```

파티션 수를 10으로 설정 하면 쿼리는 23 초 후에 종료 됩니다. 

```kusto
lineitem    
| summarize hint.strategy = shuffle hint.num_partitions = 10 dcount(l_comment), dcount(l_shipdate) by l_partkey 
| consume
```

다음 예에서는 순서 섞기 `join` 에서 성능을 크게 향상 시키는 방법을 보여 줍니다.

이 예제는 데이터가 이러한 모든 노드에 걸쳐 분산 된 10 개의 노드가 있는 클러스터에서 샘플링 되었습니다.

왼쪽 테이블에는 `join` 키의 카디널리티가 ~ 14M 인 15M 레코드가 있으며 오른쪽 `join` 은 150m 레코드이 고 `join` 키의 카디널리티는 10M입니다.
의 일반 전략 `join`을 실행 하면 쿼리는 28 초 후에 종료 되 고 메모리 사용량의 최대 사용량은 1.43입니다.

```kusto
customer
| join
    orders
on $left.c_custkey == $right.o_custkey
| summarize sum(c_acctbal) by c_nationkey
```

순서 섞기 `join` 전략을 사용 하는 동안 쿼리는 ~ 4 초 후에 종료 되 고 메모리 사용량의 최대 사용량은 0.3 g b입니다.

```kusto
customer
| join
    hint.strategy = shuffle orders
on $left.c_custkey == $right.o_custkey
| summarize sum(c_acctbal) by c_nationkey
```

의 `join` 왼쪽이 150m이 고 키의 카디널리티가 148m 인 큰 데이터 집합에서 동일한 쿼리를 시도 하는 경우의 오른쪽 `join` 은 1.5 b이 고 키의 카디널리티는 ~ 100M입니다.

기본 `join` 전략을 사용 하는 쿼리는 4 분 후에 kusto 제한 및 시간 제한에 도달 합니다.
순서 섞기 `join` 전략을 사용 하는 동안 쿼리는 ~ 34 초 후에 종료 되 고 메모리 사용량의 최대 사용량은 1.23입니다.


다음 예에서는 2 개의 클러스터 노드를 포함 하는 클러스터에 대 한 향상 된 기능을 보여 줍니다. 테이블에는 60M 레코드가 있고 `join` 키의 카디널리티는 2M입니다.
을 (를) `hint.num_partitions` 사용 하지 않고 쿼리를 실행 하면 두 개의 파티션 (클러스터 노드 번호)만 사용 하 고 다음 쿼리는 ~ 1:10 분이 소요 됩니다.

```kusto
lineitem
| summarize dcount(l_comment), dcount(l_shipdate) by l_partkey
| join
    hint.shufflekey = l_partkey   part
on $left.l_partkey == $right.p_partkey
| consume
```

파티션 수를 10으로 설정 하면 쿼리는 23 초 후에 종료 됩니다. 

```kusto
lineitem
| summarize dcount(l_comment), dcount(l_shipdate) by l_partkey
| join
    hint.shufflekey = l_partkey  hint.num_partitions = 10    part
on $left.l_partkey == $right.p_partkey
| consume
```
