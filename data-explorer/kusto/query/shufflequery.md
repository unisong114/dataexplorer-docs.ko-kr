---
title: 쿼리 순서 섞기-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 순서 섞기 쿼리를 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: a7f9388b85b673237ca676f828fc093b01b2e69d
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92241929"
---
# <a name="shuffle-query"></a>쿼리 순서 섞기

순서 섞기 쿼리는 순서 섞기 전략을 지 원하는 연산자 집합에 대 한 의미 체계 유지 변환입니다. 실제 데이터에 따라이 쿼리는 훨씬 더 나은 성능을 얻을 수 있습니다.

Kusto에서 순서 섞기를 지 원하는 연산자는 [조인](joinoperator.md), [요약](summarizeoperator.md)및 [시리즈 시리즈](make-seriesoperator.md)입니다.

쿼리 매개 변수 또는를 사용 하 여 순서 섞기 쿼리 전략을 설정 `hint.strategy = shuffle` `hint.shufflekey = <key>` 합니다.

## <a name="syntax"></a>구문

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

이 전략은 모든 클러스터 노드의 부하를 공유 합니다. 여기서 각 노드는 데이터의 단일 파티션을 처리 합니다.
키 ( `join` 키, `summarize` 키 또는 `make-series` 키)가 높은 카디널리티와 일반적인 쿼리 전략이 쿼리 제한에 도달할 경우 순서 섞기 쿼리 전략을 사용 하는 것이 유용 합니다.

**힌트 간 차이입니다. 전략 = 순서 섞기와 shufflekey = key**

`hint.strategy=shuffle` 섞은 연산자가 모든 키로 섞은 됨을 의미 합니다.
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

복합 키가 너무 고유 하지만 각 키가 충분 하지 않은 경우이 키를 사용 `hint` 하 여 섞은 연산자의 모든 키로 데이터 순서를 섞기 합니다.
섞은 연산자에 또는와 같은 다른 순서 섞기 연산자가 있는 경우에 `summarize` `join` 는 쿼리가 좀 더 복잡 해지고 힌트를 적용 합니다. 전략 = 순서 섞기는 적용 되지 않습니다.

예:

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

`hint.strategy=shuffle`쿼리 계획 중에 전략을 무시 하는 대신를 적용 하 여 복합 키 [,]를 기준으로 데이터 순서 `ActivityId` 를 변경한 경우 `numeric_column` 결과가 올바르지 않습니다.
연산자 `summarize` 가 연산자의 왼쪽에 `join` 있습니다. 이 연산자는 키의 하위 집합을 기준으로 그룹화 됩니다 `join` .이 경우는 `ActivityId` 입니다. 따라서는 `summarize` 키를 기준으로 그룹화 되는 `ActivityId` 반면 데이터는 복합 키 [,]로 분할 `ActivityId` 됩니다 `numeric_column` .
복합 키 [ `ActivityId` ,]로 순서 섞기 `numeric_column` 키에 대 한 순서 섞기가 `ActivityId` 유효 하 고 결과가 올바르지 않을 수 있습니다.

이 예에서는 복합 키에 사용 되는 해시 함수를 `binary_xor(hash(key1, 100) , hash(key2, 100))` 다음과 같이 가정 합니다.

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

두 레코드에 대 한 복합 키가 다른 파티션 (56 및 65)에 매핑 되었지만 이러한 두 레코드의 값은 동일 `ActivityId` 합니다. `summarize`의 왼쪽에 있는 연산자는 `join` 동일한 파티션에 있는 열의 유사한 값을 예상 `ActivityId` 합니다. 이 쿼리는 잘못 된 결과를 생성 합니다.

를 사용 하 여 `hint.shufflekey` 에 대 한 조인에서 순서 섞기 키를 지정 하면이 문제를 해결할 수 있습니다 `hint.shufflekey = ActivityId` . 이 키는 모든 무작위 재생 가능 연산자에 공통적입니다.
이 경우와는 모두 `join` `summarize` 동일한 키로 순서를 순서 섞기 때문에 안전 하지 않습니다. 따라서 유사한 모든 값이 동일한 파티션에 있고 결과가 올바릅니다.

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

순서 섞기 쿼리에서 기본 파티션 번호는 클러스터 노드 번호입니다. 이 숫자는 파티션 수를 제어 하는 구문을 사용 하 여 재정의할 수 있습니다 `hint.num_partitions = total_partitions` .

이 힌트는 기본 파티션 번호가 작고 쿼리가 여전히 실패 하거나 실행 시간이 길어질 때 클러스터에 적은 수의 클러스터 노드가 있는 경우에 유용 합니다.

> [!Note]
> 많은 파티션이 있으면 클러스터 리소스를 더 많이 사용 하 고 성능이 저하 될 수 있습니다. 대신 힌트를 사용 하 여 파티션 번호를 신중 하 게 선택 합니다. 전략 = 순서 섞기 및 파티션을 점차적으로 늘립니다.

## <a name="examples"></a>예

다음 예에서는 순서 섞기에서 `summarize` 성능을 크게 향상 시키는 방법을 보여 줍니다.

원본 테이블에는 150M 레코드가 있고 group by key의 카디널리티는 10 개의 클러스터 노드에 분산 된 10M입니다.

정기 `summarize` 전략을 실행 하면 쿼리가 1:08 후에 종료 되 고 메모리 사용의 피크는 ~ 3gb가 됩니다.

```kusto
orders
| summarize arg_max(o_orderdate, o_totalprice) by o_custkey 
| where o_totalprice < 1000
| count
```

|개수|
|---|
|1086|

순서 섞기 전략을 사용 하는 동안 `summarize` 쿼리는 ~ 7 초 후에 종료 되 고 메모리 사용량의 최대 사용량은 0.43입니다.

```kusto
orders
| summarize hint.strategy = shuffle arg_max(o_orderdate, o_totalprice) by o_custkey 
| where o_totalprice < 1000
| count
```

|개수|
|---|
|1086|

다음 예에서는 두 개의 클러스터 노드가 있고, 테이블에 60M 레코드가 있고, group by key의 카디널리티가 2M 인 클러스터의 향상 된 기능을 보여 줍니다.

를 사용 하지 않고 쿼리를 실행 `hint.num_partitions` 하면 두 개의 파티션 (클러스터 노드 번호)만 사용 하 고 다음 쿼리는 ~ 1:10 분이 소요 됩니다.

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

다음 예에서는 순서 섞기에서 `join` 성능을 크게 향상 시키는 방법을 보여 줍니다.

이 예제는 데이터가 이러한 모든 노드에 걸쳐 분산 된 10 개의 노드가 있는 클러스터에서 샘플링 되었습니다.

왼쪽 테이블에는 키의 카디널리티가 `join` ~ 14M 인 15M 레코드가 있습니다. 의 오른쪽은 `join` 150M 레코드를 사용 하 고 키의 카디널리티는 `join` 10M입니다.
의 일반 전략을 실행 하면 `join` 쿼리는 28 초 후에 종료 되 고 메모리 사용량의 최대 사용량은 1.43입니다.

```kusto
customer
| join
    orders
on $left.c_custkey == $right.o_custkey
| summarize sum(c_acctbal) by c_nationkey
```

순서 섞기 전략을 사용 하는 동안 `join` 쿼리는 ~ 4 초 후에 종료 되 고 메모리 사용량의 최대 사용량은 0.3입니다.

```kusto
customer
| join
    hint.strategy = shuffle orders
on $left.c_custkey == $right.o_custkey
| summarize sum(c_acctbal) by c_nationkey
```

의 좌 변에 `join` 150M이 고 키의 카디널리티가 148M 인 큰 데이터 집합에 대해 동일한 쿼리를 시도 합니다. 의 오른쪽은 `join` 1.5 b이 고 키의 카디널리티는 ~ 100M입니다.

기본 전략을 사용 하는 쿼리는 `join` 4 분 후에 Kusto 제한 및 시간 제한에 도달 합니다.
순서 섞기 전략을 사용 하는 동안 `join` 쿼리는 ~ 34 초 후에 종료 되 고 메모리 사용량의 최대 사용량은 1.23 GB입니다.


다음 예에서는 두 개의 클러스터 노드가 있고, 테이블에 60M 레코드가 있고, 키의 카디널리티가 2M 클러스터에 대 한 향상 된 기능을 보여 줍니다 `join` .
를 사용 하지 않고 쿼리를 실행 `hint.num_partitions` 하면 두 개의 파티션 (클러스터 노드 번호)만 사용 하 고 다음 쿼리는 ~ 1:10 분이 소요 됩니다.

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
