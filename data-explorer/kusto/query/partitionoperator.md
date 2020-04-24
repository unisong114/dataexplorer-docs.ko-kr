---
title: 파티션 연산자 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 파티션 연산자에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 0a9ef31f68a989fffe42d9b54800e9305e51f4ed
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81511376"
---
# <a name="partition-operator"></a>partition 연산자

파티션 연산자는 지정된 열의 값에 따라 입력 테이블을 여러 하위 테이블로 분할하고, 각 하위 테이블에 대한 하위 쿼리를 실행하고, 모든 하위 쿼리 결과의 조합인 단일 출력 테이블을 생성합니다. 

```kusto
T | partition by Col1 ( top 10 by MaxValue )

T | partition by Col1 { U | where Col2=toscalar(Col1) }
```

**구문**

*T* `|` T `partition` [*파티션 매개 변수]* `by` *열* `(` *컨텍스트하위 쿼리*`)`

*T* `|` T `partition` [*파티션 매개 변수*] `by` *열* `{` *하위 쿼리*`}`

**인수**

* *T*: 운영자가 데이터를 처리할 테이블 형식 원본입니다.

* *열*: 값이 입력 테이블을 분할하는 방법을 결정하는 *T의* 열 이름입니다. 아래 **참고 사항**

* *ContextualSubquery*: 단일 *키* 값에 대해 범위가 `partition` 조정된 소스인 테이블 형식식입니다.

* 하위 쿼리 : 소스가 없는 테이블 형식 *식입니다.* *키* 값은 호출을 `toscalar()` 통해 얻을 수 있습니다.

* *PartitionParameters*: 연산자의 동작을 제어하는 *이름* `=` *값의* 형태로 0 개 이상의 (공간 분리) 매개 변수입니다. 다음 매개 변수가 지원됩니다.

  |이름               |값         |Description|
  |-------------------|---------------|-----------|
  |`hint.materialized`|`true`,`false` |연산자의 `true` 소스를 구체화하도록 설정하면(기본값: `false`) `partition`|
  |`hint.concurrency`|*수*|연산자의 동시 하위 쿼리 수를 병렬로 `partition` 실행해야 하는지 시스템을 암시합니다. *기본값*: 클러스터의 단일 노드에 있는 CPU 코어의 양(2~16)입니다.|
  |`hint.spread`|*수*|동시`partition` 하위 쿼리 실행에서 사용해야 하는 노드 수를 시스템에 암시합니다. *기본값*: 1.|

**반환**

연산자는 입력 데이터의 각 파티션에 하위 쿼리를 적용한 결과의 공용 구조체를 반환합니다.

**참고 사항**

* 파티션 연산자는 현재 파티션 수에 의해 제한됩니다.
  최대 64개의 서로 다른 파티션을 만들 수 있습니다.
  파티션*열(Column)에*64개 이상의 고유 값이 있는 경우 연산자가 오류를 생성합니다.

* 하위 쿼리는 입력 파티션을 암시적으로 참조합니다(하위 쿼리의 파티션에 대한 "name"은 없습니다). 하위 쿼리 내에서 입력 파티션을 여러 번 참조하려면 **예제:** 아래 파티션 참조에서와 같이 [연산자로](asoperator.md)사용합니다.

**예: 상위 중첩 케이스**

경우에 따라 `partition` - 연산자 대신 [ `top-nested` 연산자를](topnestedoperator.md) 사용하여 쿼리를 작성하는 것이 더 `summarize` 성능이 뛰어나고 쉽게 다음 예제는 하위 쿼리 계산을 `top` 실행하고 각 주에 대해 다음과 같은 (와이오밍, `W`워싱턴, 웨스트 버지니아, 위스콘신)으로 시작합니다.

```kusto
StormEvents
| where State startswith 'W'
| partition by State 
(
    summarize Events=count(), Injuries=sum(InjuriesDirect) by EventType, State
    | top 3 by Events 
) 

```
|EventType|시스템 상태|이벤트|부상|
|---|---|---|---|
|우박|와이오밍|108|0|
|강풍|와이오밍|81|5|
|겨울 폭풍|와이오밍|72|0|
|폭설|워싱턴|82|0|
|강풍|워싱턴|58|13|
|산불|워싱턴|29|0|
|뇌우를 동반한 바람|웨스트버지니아|180|1|
|우박|웨스트버지니아|103|0|
|겨울 날씨|웨스트버지니아|88|0|
|뇌우를 동반한 바람|위스콘신|416|1|
|겨울 폭풍|위스콘신|310|0|
|우박|위스콘신|303|1|

**예: 겹치지 않는 데이터 파티션 쿼리**

때로는 맵 /reduce 스타일에서 겹치지 않는 데이터 파티션을 통해 복잡한 하위 쿼리를 실행하는 것이 유용합니다. 아래 예제에서는 10개 이상의 파티션에 대한 집계의 수동 분포를 만드는 방법을 보여 주어 있습니다.

```kusto
StormEvents
| extend p = hash(EventId, 10)
| partition by p
(
    summarize Count=count() by Source 
)
| summarize Count=sum(Count) by Source
| top 5 by Count
```

|원본|개수|
|---|---|
|숙련된 관찰자|12770|
|사법 기관|8570|
|Public|6157|
|긴급 상황 관리자|4900|
|협동 관찰자|3039|

**예: 쿼리 시간 분할**

다음 예제에서는 쿼리를 N=10 파티션으로 분할하는 방법, 각 파티션이 자체 개수를 계산하고 나중에 TotalCount로 요약되는 방법을 보여 주며, 이 파티션은 모두 나중에 TotalCount로 요약됩니다.

```kusto
let N = 10;                 // Number of query-partitions
range p from 0 to N-1 step 1  // 
| partition by p            // Run the sub-query partitioned 
{
    StormEvents 
    | where hash(EventId, N) == toscalar(p) // Use toscalar() to fetch partition key value
    | summarize Count = count()
}
| summarize TotalCount=sum(Count) 
```

|TotalCount|
|---|
|59066|


**예: 파티션 참조**

다음 예제에서는 [연산자로](asoperator.md) 사용하여 각 데이터 파티션에 "name"을 지정한 다음 하위 쿼리 내에서 해당 이름을 다시 사용하는 방법을 보여 줍니다.

```kusto
T
| partition by Dim
(
    as Partition
    | extend MetricPct = Metric * 100.0 / toscalar(Partition | summarize sum(Metric))
)
```

**예: 함수 호출에 의해 숨겨진 복잡한 하위 쿼리**

훨씬 더 복잡한 하위 쿼리와 동일한 기술을 적용할 수 있습니다. 구문을 단순화하기 위해 함수 호출에서 하위 쿼리를 래핑할 수 있습니다.

```kusto
let partition_function = (T:(Source:string)) 
{
    T
    | summarize Count=count() by Source
};
StormEvents
| extend p = hash(EventId, 10)
| partition by p
(
    invoke partition_function()
)
| summarize Count=sum(Count) by Source
| top 5 by Count
```

|원본|개수|
|---|---|
|숙련된 관찰자|12770|
|사법 기관|8570|
|Public|6157|
|긴급 상황 관리자|4900|
|협동 관찰자|3039|