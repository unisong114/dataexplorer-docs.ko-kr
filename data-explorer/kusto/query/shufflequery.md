---
title: 셔플 쿼리 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 셔플 쿼리에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: c687d495a41a5f73ac8dbca15d93729f2132a556
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81662984"
---
# <a name="shuffle-query"></a>셔플 쿼리

셔플 쿼리는 실제 데이터에 따라 훨씬 더 나은 성능을 얻을 수 있는 셔플 전략을 지원하는 연산자 집합에 대한 의미 체계 보존 변환입니다.

Kusto에서 셔플을 지원하는 연산자는 [조인,](joinoperator.md) [요약](summarizeoperator.md) 및 [메이크 시리즈입니다.](make-seriesoperator.md)

셔플 쿼리 전략은 쿼리 매개 `hint.strategy = shuffle` `hint.shufflekey = <key>`변수 또는 에 의해 설정할 수 있습니다.

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

이 전략은 각 노드가 데이터의 하나의 파티션을 처리하는 모든 클러스터 노드의 부하를 공유합니다.
키(키,`join` `summarize` 키 또는 `make-series` 키)가 카디널리티가 높은 경우 일반 쿼리 전략이 쿼리 한계에 도달할 때 셔플 쿼리 전략을 사용하는 것이 유용합니다.

**힌트.전략=셔플과 힌트.셔플키 = 키의 차이**

`hint.strategy=shuffle`즉, 섞인 연산자가 모든 키로 섞이게 됩니다.
예를 들어, 이 쿼리에서 :

```kusto
T | where Event=="Start" | project ActivityId, Started=Timestamp
| join hint.strategy = shuffle (T | where Event=="End" | project ActivityId, Ended=Timestamp)
  on ActivityId, ProcessId
| extend Duration=Ended - Started
| summarize avg(Duration)
```

데이터를 섞는 해시 함수는 ActivityId 키와 ProcessId 키를 모두 사용합니다.

위의 쿼리는 다음과 같습니다.

```kusto
T | where Event=="Start" | project ActivityId, Started=Timestamp
| join hint.shufflekey = ActivityId hint.shufflekey = ProcessId (T | where Event=="End" | project ActivityId, Ended=Timestamp)
  on ActivityId, ProcessId
| extend Duration=Ended - Started
| summarize avg(Duration)
```

이 힌트는 복합 키가 너무 고유하지만 각 키가 충분히 고유하지 않기 때문에 섞인 연산자의 모든 키로 데이터를 섞는 데 관심이 있을 때 사용할 수 있습니다.
셔플 연산자가 `summarize` 같은 다른 셔플 연산자가 있는 경우 또는 `join`, 쿼리가 더 복잡해지고 hint.strategy=셔플이 적용되지 않습니다.

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

이 경우 `hint.strategy=shuffle` 쿼리 계획 중에 전략을 무시하는 대신 적용하고 복합 키로`ActivityId` `numeric_column`데이터를 섞으면 결과가 올바르지 않습니다.
있는 키의 하위 집합에 의해 그루브의 `join` 왼쪽에 있는 . `ActivityId` `summarize` `join` 데이터가 복합 `summarize` 키 `ActivityId` [,`ActivityId` `numeric_column`]에 의해 분할되는 동안 키에 의해 그룹화하는 것을 의미한다.
복합 키 [,`ActivityId` `numeric_column`] 로 섞는다는 것은 키 ActivityId에 대한 유효한 셔플링이며 결과가 올바르지 않을 수 있음을 의미하지는 않습니다.

이 예제는 복합 키에 사용되는 해시 함수가`binary_xor(hash(key1, 100) , hash(key2, 100))`

```kusto

datatable(ActivityId:string, NumericColumn:long)
[
"activity1", 2,
"activity1" ,1,
]
| extend hash_by_key = binary_xor(hash(ActivityId, 100) , hash(NumericColumn, 100))
```

|ActivityId|숫자열|hash_by_key|
|---|---|---|
|활동1|2|56|
|활동1|1|65|



두 레코드의 복합 키가 서로 다른 파티션 56및 65에 매핑되었지만 이 두 `ActivityId` 레코드의 `summarize` 왼쪽에 `join` 있는 열의 왼쪽에 동일한 `ActivityId` 파티션이 동일한 파티션에 있을 것으로 예상되는 동일한 값을 가지므로 잘못된 결과가 생성됩니다.

이 경우 `hint.shufflekey` 모든 셔플 가능한 연산자의 공통 키인 조인에 셔플 키를 `hint.shufflekey = ActivityId` 지정하여 이 문제를 해결합니다.
이 경우 셔플은 `join` 안전하며 `summarize` 동일한 키로 섞어 모든 유사한 값이 동일한 파티션에 있을 수 있으므로 결과가 정확합니다.

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

|ActivityId|숫자열|hash_by_key|
|---|---|---|
|활동1|2|56|
|활동1|1|65|

셔플 쿼리에서 기본 파티션 번호는 클러스터 노드 번호입니다. 이 숫자는 파티션 수를 제어하는 구문을 `hint.num_partitions = total_partitions` 사용하여 재정의할 수 있습니다.

이 힌트는 클러스터에 기본 파티션 수가 너무 작고 쿼리가 여전히 실패하거나 실행 시간이 오래 걸리는 클러스터 노드 수가 적은 경우에 유용합니다.

많은 파티션을 설정하면 성능이 저하되고 더 많은 클러스터 리소스가 소비될 수 있으므로 파티션 번호를 신중하게 선택하는 것이 좋습니다(hint.strategy = 셔플으로 시작하여 파티션을 점진적으로 늘리기 시작).

**예**

다음 예제에서는 셔플이 `summarize` 성능을 상당히 향상시키는 방법을 보여 주며 있습니다.

원본 테이블에는 150M 레코드가 있으며 키별 그룹의 카디널리티는 10개 클러스터 노드에 분산되어 있는 10M입니다.

일반 `summarize` 전략을 실행하면 쿼리가 1:08 이후에 종료되고 메모리 사용량 피크는 ~3GB입니다.

```kusto
orders
| summarize arg_max(o_orderdate, o_totalprice) by o_custkey 
| where o_totalprice < 1000
| count
```

|개수|
|---|
|1086|

셔플 `summarize` 전략을 사용하는 동안 쿼리는 ~ 7초 후에 끝나고 메모리 사용량피크는 0.43GB입니다.

```kusto
orders
| summarize hint.strategy = shuffle arg_max(o_orderdate, o_totalprice) by o_custkey 
| where o_totalprice < 1000
| count
```

|개수|
|---|
|1086|

다음 예제에서는 2개의 클러스터 노드가 있고 테이블에 60M 레코드가 있고 키별 그룹의 카디널리티가 2M인 클러스터의 개선을 보여 주습니다.

없이 쿼리를 `hint.num_partitions` 실행 하면 2 개의 파티션 (클러스터 노드 번호)만 사용 하 고 다음 쿼리 ~1:10 분 걸릴 것입니다.

```kusto
lineitem    
| summarize hint.strategy = shuffle dcount(l_comment), dcount(l_shipdate) by l_partkey 
| consume
```

파티션 번호를 10으로 설정하면 쿼리가 23초 후에 종료됩니다. 

```kusto
lineitem    
| summarize hint.strategy = shuffle hint.num_partitions = 10 dcount(l_comment), dcount(l_shipdate) by l_partkey 
| consume
```

다음 예제에서는 셔플이 `join` 성능을 상당히 향상시키는 방법을 보여 주며 있습니다.

예제는 데이터가 이러한 모든 노드에 분산되는 10개의 노드가 있는 클러스터에서 샘플링되었습니다.

왼쪽 테이블에는 `join` 키의 카디널리티가 ~14M인 15M 레코드가 `join` 있고, 오른쪽은 150M 레코드이고 `join` 키의 카디널리티는 10M입니다.
`join`의 일반 전략을 실행하면 ~ 28 초 후에 쿼리가 끝나고 메모리 사용량 피크는 1.43GB입니다.

```kusto
customer
| join
    orders
on $left.c_custkey == $right.o_custkey
| summarize sum(c_acctbal) by c_nationkey
```

셔플 `join` 전략을 사용하는 동안 쿼리는 ~ 4 초 후에 끝나고 메모리 사용량 피크는 0.3GB입니다.

```kusto
customer
| join
    hint.strategy = shuffle orders
on $left.c_custkey == $right.o_custkey
| summarize sum(c_acctbal) by c_nationkey
```

키의 `join` 왼쪽이 150M이고 키의 카디널리티가 148M이고 오른쪽이 1.5B이고 키의 `join` 카디널리티가 ~100M인 더 큰 데이터 집합에서 동일한 쿼리를 시도합니다.

기본 `join` 전략이 있는 쿼리는 4분 후에 kusto 제한 및 시간 제한을 맞습니다.
셔플 `join` 전략을 사용하는 동안 쿼리는 ~ 34초 후에 끝나고 메모리 사용량은 1.23GB입니다.


다음 예제에서는 2개의 클러스터 노드가 있고 테이블에 60M 레코드가 있고 `join` 키의 카디널리티가 2M인 클러스터의 개선을 보여 주며, 이 예제에서는 2M입니다.
없이 쿼리를 `hint.num_partitions` 실행 하면 2 개의 파티션 (클러스터 노드 번호)만 사용 하 고 다음 쿼리 ~1:10 분 걸릴 것입니다.

```kusto
lineitem
| summarize dcount(l_comment), dcount(l_shipdate) by l_partkey
| join
    hint.shufflekey = l_partkey   part
on $left.l_partkey == $right.p_partkey
| consume
```

파티션 번호를 10으로 설정하면 쿼리가 23초 후에 종료됩니다. 

```kusto
lineitem
| summarize dcount(l_comment), dcount(l_shipdate) by l_partkey
| join
    hint.shufflekey = l_partkey  hint.num_partitions = 10    part
on $left.l_partkey == $right.p_partkey
| consume
```