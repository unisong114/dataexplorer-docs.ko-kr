---
title: 파티션 연산자-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 파티션 연산자에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 417d4afb74e9170301baebde6be73d97df097f0f
ms.sourcegitcommit: 733bde4c6bc422c64752af338b29cd55a5af1f88
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/13/2020
ms.locfileid: "83271539"
---
# <a name="partition-operator"></a>partition 연산자

Partition 연산자는 지정 된 열의 값에 따라 입력 테이블을 여러 하위 테이블로 분할 하 고, 각 하위 테이블에 대해 하위 쿼리를 실행 하 고, 모든 하위 쿼리 결과의 합집합에 해당 하는 단일 출력 테이블을 생성 합니다. 

```kusto
T | partition by Col1 ( top 10 by MaxValue )

T | partition by Col1 { U | where Col2=toscalar(Col1) }
```

**구문**

*T* `|` `partition` [*파티션 매개 변수*] `by` *열* `(` *ContextualSubquery*`)`

*T* `|` `partition` [*파티션 매개 변수*] `by` *열* `{` *하위 쿼리*`}`

**인수**

* *T*: 연산자에서 처리할 데이터를 포함 하는 테이블 형식 원본입니다.

* *열*: 값이 입력 테이블을 분할 하는 방법을 결정 하는 *T* 의 열 이름입니다. 아래 **참고** 를 참조 하세요.

* *ContextualSubquery*: 테이블 형식 식이 며,이는 `partition` 단일 *키* 값에 대해 범위가 지정 된 연산자의 원본입니다.

* *하위 쿼리*: 원본이 없는 테이블 형식 식입니다. 호출을 통해 *키* 값을 가져올 수 있습니다 `toscalar()` .

* *파티션 매개 변수*: *Name* `=` 연산자의 동작을 제어 하는 이름 *값* 형식의 0 개 이상의 (공백으로 구분 된) 매개 변수입니다. 지원 되는 매개 변수는 다음과 같습니다.

  |속성               |값         |Description|
  |-------------------|---------------|-----------|
  |`hint.materialized`|`true`,`false` |로 설정 하면 `true` 연산자의 소스가 구체화 됩니다 `partition` (기본값:). `false`|
  |`hint.concurrency`|*숫자*|시스템 힌트 병렬로 실행 해야 하는 연산자의 동시 하위 쿼리 수 `partition` 입니다. *기본값*: 클러스터의 단일 노드 (2-16)에 있는 CPU 코어의 양입니다.|
  |`hint.spread`|*숫자*|동시 하위 쿼리 실행에서 사용 해야 하는 노드 수를 시스템에 설명 합니다 `partition` . *기본값*: 1.|

**반환**

연산자는 하위 쿼리를 입력 데이터의 각 파티션에 적용 한 결과의 합집합을 반환 합니다.

**참고 사항**

* 파티션 연산자는 현재 파티션 수에 의해 제한 됩니다.
  최대 64 개의 고유 파티션을 만들 수 있습니다.
  파티션 열 (*열*)에 64 개 이상의 고유 값이 있는 경우 연산자는 오류를 생성 합니다.

* 하위 쿼리는 입력 파티션을 암시적으로 참조 합니다. 하위 쿼리의 파티션에는 "name"이 없습니다. 하위 쿼리 내에서 입력 파티션을 여러 번 참조 하려면 아래 **예제** 와 같이 [as 연산자](asoperator.md)를 사용 합니다.

**예: 상위 중첩 된 사례**

경우에 따라 연산자를 사용 하는 연산자를 사용 하 여 쿼리를 작성 하는 것이 더 빠르고 쉬우며 연산자를 사용 하 여 쿼리를 작성할 수 `partition` 있습니다. 다음 예제에서는 하위 쿼리 계산을 실행 하 [ `top-nested` ](topnestedoperator.md) `summarize` 고 `top` `W` (와이오밍, 워싱턴, 서쪽 버지니아, 위스콘신)

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents
| where State startswith 'W'
| partition by State 
(
    summarize Events=count(), Injuries=sum(InjuriesDirect) by EventType, State
    | top 3 by Events 
) 

```
|EventType|State|이벤트|부상|
|---|---|---|---|
|우박|와이오밍|108|0|
|높은 바람|와이오밍|81|5|
|겨울 스톰|와이오밍|72|0|
|눈 덮인|워싱턴|82|0|
|높은 바람|워싱턴|58|13|
|산|워싱턴|29|0|
|뇌우를 동반한 바람|웨스트버지니아|180|1|
|우박|웨스트버지니아|103|0|
|겨울 날씨|웨스트버지니아|88|0|
|뇌우를 동반한 바람|위스콘신|416|1|
|겨울 스톰|위스콘신|310|0|
|우박|위스콘신|303|1|

**예: 겹치지 않는 데이터 파티션 쿼리**

맵/감소 스타일의 겹치지 않는 데이터 파티션에 대해 복잡 한 하위 쿼리를 실행 하는 것이 유용한 경우도 있습니다. 아래 예제에서는 10 개의 파티션에 대 한 집계의 수동 배포를 만드는 방법을 보여 줍니다.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
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
|공용|6157|
|긴급 상황 관리자|4900|
|COOP 관찰자|3039|

**예: 쿼리 시간 분할**

다음 예에서는 쿼리를 N = 10 개의 파티션으로 분할 하는 방법을 보여 줍니다. 여기서 각 파티션은 고유 카운트를 계산 하 고 나중에 TotalCount에 요약 됩니다.

<!-- csl: https://help.kusto.windows.net/Samples -->
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


**예: 파티션-참조**

다음 예에서는 [as 연산자](asoperator.md) 를 사용 하 여 각 데이터 파티션에 "이름"을 지정한 다음 하위 쿼리 내에서 해당 이름을 다시 사용 하는 방법을 보여 줍니다.

```kusto
T
| partition by Dim
(
    as Partition
    | extend MetricPct = Metric * 100.0 / toscalar(Partition | summarize sum(Metric))
)
```

**예: 함수 호출로 숨겨진 복합 하위 쿼리**

훨씬 더 복잡 한 하위 쿼리를 사용 하 여 동일한 기법을 적용할 수 있습니다. 구문을 단순화 하기 위해 함수 호출에서 하위 쿼리를 래핑할 수 있습니다.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
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
|공용|6157|
|긴급 상황 관리자|4900|
|COOP 관찰자|3039|