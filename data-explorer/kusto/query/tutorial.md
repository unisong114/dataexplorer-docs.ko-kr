---
title: 자습서-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 자습서를 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/08/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 8a3c0b058b2c1cf5023ce0069a7dd938fce5caec
ms.sourcegitcommit: 4c7f20dfd59fb5b5b1adfbbcbc9b7da07df5e479
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/23/2020
ms.locfileid: "95324740"
---
# <a name="tutorial"></a>자습서

::: zone pivot="azuredataexplorer"

Kusto 쿼리 언어를 배우는 가장 좋은 방법은 몇 가지 [샘플 데이터를 포함](https://help.kusto.windows.net/Samples)하는 데이터베이스를 사용 하 여 언어에 대 한 "느낌"을 얻는 간단한 쿼리를 살펴보는 것입니다. 이 문서에서 설명 하는 쿼리는 해당 데이터베이스에서 실행 되어야 합니다. `StormEvents`이 샘플 데이터베이스의 테이블은 미국에서 발생 하는 폭풍에 대 한 일부 정보를 제공 합니다.

## <a name="count-rows"></a>행 수 계산

예제 데이터베이스에는 라는 테이블이 있습니다 `StormEvents` .
크기를 확인 하기 위해 해당 콘텐츠를 단순히 행 수를 계산 하는 연산자로 파이프 합니다.

* *구문:* 쿼리는 데이터 원본 (일반적으로 테이블 이름)으로, 선택적으로 하나 이상의 파이프 문자 쌍 및 일부 테이블 형식 연산자가 올 수 있습니다.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents | count
```

결과:

|개수|
|-----|
|59066|
    
[count 연산자](./countoperator.md).

## <a name="project-select-a-subset-of-columns"></a>프로젝트: 열의 하위 집합 선택

[Project](./projectoperator.md) 를 사용 하 여 원하는 열만 선택 합니다. [Project](./projectoperator.md) 및 [take](./takeoperator.md) 연산자를 모두 사용 하는 아래 예제를 참조 하세요.

## <a name="where-filtering-by-a-boolean-expression"></a>where: 부울 식으로 필터링

`flood` `California` 2 월-2007 중의만 표시 해 보겠습니다.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| where StartTime > datetime(2007-02-01) and StartTime < datetime(2007-03-01)
| where EventType == 'Flood' and State == 'CALIFORNIA'
| project StartTime, EndTime , State , EventType , EpisodeNarrative
```

|StartTime|EndTime|시스템 상태|EventType|EpisodeNarrative|
|---|---|---|---|---|
|2007-02-19 00:00:00.0000000|2007-02-19 08:00:00.0000000|캘리포니아|홍수|남부 San Joaquin 계곡을 통해 전면 시스템을 이동 하는 경우 19 일의 초기 아침 시간에 매우 복잡 한 서유럽 관할지의 짧은 기간이 소요 됩니다. 166 고속도로 Taft near 상태에서 사소한 초과가 보고 되었습니다.|

## <a name="take-show-me-n-rows"></a>take: n 개 행 표시

몇 가지 데이터를 살펴보겠습니다. 샘플 5 행에는 무엇이 있나요?

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| take 5
| project  StartTime, EndTime, EventType, State, EventNarrative  
```

|StartTime|EndTime|EventType|시스템 상태|EventNarrative|
|---|---|---|---|---|
|2007-09-18 20:00:00.0000000|2007-09-19 18:00:00.0000000|과도 한 비|플로리다|Coastal Volusia 군의 일부에 걸쳐 24 시간 동안 9 인치의 과도 한가 있습니다.|
|2007-09-20 21:57:00.0000000|2007-09-20 22:05:00.0000000|토네이도|플로리다|토네이도는 서쪽 Crooked Lake의 북쪽 끝에 있는 Eustis 마을에서 종료 되었습니다. 토네이도는 북쪽 북서쪽에서 Eustis로 이동함에 따라 EF1 강도로 빠르게 intensified. 트랙은 길이가 2 마일 미만 이며 최대 너비가 300 야드입니다.  토네이도는 7 개의 홈을 제거 했습니다. 25 개의 집에서 주요 데미지와 81 홈이 손상 된 부분을 수신 했습니다. $620만에는 심각한 부상 및 속성 손상이 설정 되지 않았습니다.|
|2007-09-29 08:11:00.0000000|2007-09-29 08:11:00.0000000|Waterspout|대서양 남부|Waterspout의 대서양 남동쪽에서 형성 된이 고, 쇼로 잠시 이동 했습니다.|
|2007-12-20 07:50:00.0000000|2007-12-20 07:53:00.0000000|뇌우를 동반한 바람|MISSISSIPPI|많은 초대형 트리가 약간 다운 되었습니다. 동부 Adams 군에서 손상이 발생 했습니다.|
|2007-12-30 16:00:00.0000000|2007-12-30 16:05:00.0000000|뇌우를 동반한 바람|그루지야|군 Batten 루프 206 근처의 Quincey 루프를 따라 몇 개의 트리가 보고 되었습니다. 트리 제거 비용이 예상 됩니다.|

하지만 [는](./takeoperator.md) 테이블의 행을 특정 순서로 표시 하지 않으므로 정렬 하겠습니다.
* [제한은](./takeoperator.md) [사용](./takeoperator.md) 에 대 한 별칭이 며 동일한 효과를 가집니다.

## <a name="sort-and-top"></a>정렬 및 위쪽

* *구문:* 일부 연산자에는와 같은 키워드에서 도입 된 매개 변수가 있습니다 `by` .
* `desc`은(는) 내림차순을 의미하고 `asc`은(는) 오름차순을 의미합니다.

특정 열을 기준으로 순서가 정해진 처음 n 행 표시:

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| top 5 by StartTime desc
| project  StartTime, EndTime, EventType, State, EventNarrative  
```

|StartTime|EndTime|EventType|시스템 상태|EventNarrative|
|---|---|---|---|---|
|2007-12-31 22:30:00.0000000|2007-12-31 23:59:00.0000000|겨울 스톰|미시간|이 매우 심각한 이벤트는 새 해의 일에 대 한 초기 아침 시간으로 계속 됩니다.|
|2007-12-31 22:30:00.0000000|2007-12-31 23:59:00.0000000|겨울 스톰|미시간|이 매우 심각한 이벤트는 새 해의 일에 대 한 초기 아침 시간으로 계속 됩니다.|
|2007-12-31 22:30:00.0000000|2007-12-31 23:59:00.0000000|겨울 스톰|미시간|이 매우 심각한 이벤트는 새 해의 일에 대 한 초기 아침 시간으로 계속 됩니다.|
|2007-12-31 23:53:00.0000000|2007-12-31 23:53:00.0000000|높은 바람|캘리포니아|북쪽 ~ 북동쪽 기간 동안 gusting 58입니다는 Ventura 관할지의 산에 보고 되었습니다.|
|2007-12-31 23:53:00.0000000|2007-12-31 23:53:00.0000000|높은 바람|캘리포니아|웜 스프링 RAWS 센서가 기간 동안 gusting를 58입니다에 보고 했습니다.|

[Sort](./sortoperator.md) 를 사용 하 고 연산자를 사용 하 여 동일한 [를 수행할 수](./takeoperator.md) 있습니다.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| sort by StartTime desc
| take 5
| project  StartTime, EndTime, EventType, EventNarrative
```

## <a name="extend-compute-derived-columns"></a>확장: 파생 열 계산

모든 행의 값을 계산 하 여 새 열을 만듭니다.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| limit 5
| extend Duration = EndTime - StartTime 
| project StartTime, EndTime, Duration, EventType, State
```

|StartTime|EndTime|기간|EventType|시스템 상태|
|---|---|---|---|---|
|2007-09-18 20:00:00.0000000|2007-09-19 18:00:00.0000000|22:00:00|과도 한 비|플로리다|
|2007-09-20 21:57:00.0000000|2007-09-20 22:05:00.0000000|00:08:00|토네이도|플로리다|
|2007-09-29 08:11:00.0000000|2007-09-29 08:11:00.0000000|00:00:00|Waterspout|대서양 남부|
|2007-12-20 07:50:00.0000000|2007-12-20 07:53:00.0000000|00:03:00|뇌우를 동반한 바람|MISSISSIPPI|
|2007-12-30 16:00:00.0000000|2007-12-30 16:05:00.0000000|00:05:00|뇌우를 동반한 바람|그루지야|

열 이름을 다시 사용 하 고 계산 결과를 동일한 열에 할당할 수 있습니다.
다음은 그 예입니다. 

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print x=1
| extend x = x + 1, y = x
| extend x = x + 1
```

|x|y|
|---|---|
|3|1|

[스칼라 식](./scalar-data-types/index.md) 에는 일반적인 연산자 ( `+` ,,,,)가 모두 포함 될 수 `-` `*` `/` `%` 있으며 다양 한 유용한 함수가 있습니다.

## <a name="summarize-aggregate-groups-of-rows"></a>요약: 행 그룹 집계

각 국가에서 발생 하는 이벤트 수를 계산 합니다.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| summarize event_count = count() by State
```

절에서 동일한 값을 가진 행을 그룹화 하 여 [요약](./summarizeoperator.md) 한 `by` 다음 집계 함수 (예:)를 사용 `count` 하 여 각 그룹을 단일 행으로 결합 합니다. 따라서이 경우 각 주에 대 한 행과 해당 상태의 행 수에 대 한 열이 있습니다.

[집계 함수](./summarizeoperator.md#list-of-aggregation-functions)에는 다양 한 요약 연산자를 사용 하 여 여러 계산 열을 만들 수 있습니다. 예를 들어 각 상태의 폭풍 수와 상태별로 고유한 폭풍 유형 합계를 얻을 수 있습니다.  
그런 다음, [top](./topoperator.md) 을 사용 하 여 가장 많이 영향을 주는 상태를 가져올 수 있습니다.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents 
| summarize StormCount = count(), TypeOfStorms = dcount(EventType) by State
| top 5 by StormCount desc
```

|시스템 상태|StormCount|TypeOfStorms|
|---|---|---|
|텍사스|4701|27|
|캔자스|3166|21|
|아이오와|2337|19|
|ILLINOIS|2022|23|
|MISSOURI|2016|20|

summarize의 결과에 포함된 내용:

* `by`에 명명된 각 열
* 계산 된 각 식에 대 한 열입니다.
* 각 `by` 값 조합에 대한 행

## <a name="summarize-by-scalar-values"></a>스칼라 값에 의해 요약

절에서 스칼라 (숫자, 시간 또는 간격) 값을 사용할 수 `by` 있지만 bin에 값을 저장 하는 것이 좋습니다.  
[Bin ()](./binfunction.md) 함수는 다음과 같은 경우에 유용 합니다.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| where StartTime > datetime(2007-02-14) and StartTime < datetime(2007-02-21)
| summarize event_count = count() by bin(StartTime, 1d)
```

이렇게 하면 모든 타임 스탬프가 1 일 간격으로 줄어듭니다.

|StartTime|event_count|
|---|---|
|2007-02-14 00:00:00.0000000|180|
|2007-02-15 00:00:00.0000000|66|
|2007-02-16 00:00:00.0000000|164|
|2007-02-17 00:00:00.0000000|103|
|2007-02-18 00:00:00.0000000|22|
|2007-02-19 00:00:00.0000000|52|
|2007-02-20 00:00:00.0000000|60|

[Bin ()](./binfunction.md) 은 여러 언어로 된 [floor ()](./floorfunction.md) 함수와 동일 합니다. [요약](./summarizeoperator.md) 에서 행을 그룹에 할당할 수 있도록 사용자가 제공 하는 모듈러스의 가장 가까운 배수로 모든 값을 줄입니다.

## <a name="render-display-a-chart-or-table"></a>Render: 차트 또는 테이블을 표시 합니다.

두 열을 프로젝션 하 고 차트의 x 축과 y 축으로 사용 합니다.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents 
| summarize event_count=count(), mid = avg(BeginLat) by State 
| sort by mid
| where event_count > 1800
| project State, event_count
| render columnchart
```

:::image type="content" source="images/tutorial/event-counts-state.png" alt-text="상태별 스톰 이벤트 수의 세로 막대형 차트":::

`mid`프로젝트 작업에서을 제거 했지만 차트에서 해당 순서로 국가를 표시 하려는 경우에도이 작업이 필요 합니다.

' 렌더링 '은 쿼리 언어의 일부가 아니라 클라이언트의 기능입니다. 그래도 언어에 통합 되어 있으므로 결과를 구상 하는 데 매우 유용 합니다.


## <a name="timecharts"></a>시간 차트

숫자 bin으로 돌아가서 시계열을 표시 하겠습니다.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| summarize event_count=count() by bin(StartTime, 1d)
| render timechart
```

:::image type="content" source="images/tutorial/time-series-start-bin.png" alt-text="시간별로 범주화 된 꺾은선형 차트 이벤트":::

## <a name="multiple-series"></a>여러 계열

`summarize by` 절에 여러 값을 사용하여 각 값 조합에 대해 별도의 행을 생성합니다.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents 
| where StartTime > datetime(2007-06-04) and StartTime < datetime(2007-06-10) 
| where Source in ("Source","Public","Emergency Manager","Trained Spotter","Law Enforcement")
| summarize count() by bin(StartTime, 10h), Source
```

:::image type="content" source="images/tutorial/table-count-source.png" alt-text="원본으로 테이블 수":::

위의에 렌더링 용어를 추가 하기만 하면 `| render timechart` 됩니다.

:::image type="content" source="images/tutorial/line-count-source.png" alt-text="원본 별 꺾은선형 차트 수":::

는 `render timechart` 첫 번째 열을 x 축으로 사용 하 고 다른 열은 별도의 선으로 표시 합니다.

## <a name="daily-average-cycle"></a>일일 평균 주기

작업은 평균 일 동안 어떻게 달라 지는가?

시간별로 범주화 된 시간 모듈로 이벤트 수를 계산 합니다. Bin 대신을 사용 합니다 `floor` .

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| extend hour = floor(StartTime % 1d , 1h)
| summarize event_count=count() by hour
| sort by hour asc
| render timechart
```

:::image type="content" source="images/tutorial/time-count-hour.png" alt-text="시간별 시간 차트 수":::

현재는 `render` 기간에 레이블을 올바르게 나타내지 않지만를 대신 사용할 수 있습니다 `| render columnchart` .

:::image type="content" source="images/tutorial/column-count-hour.png" alt-text="시간별 세로 막대형 차트 수":::

## <a name="compare-multiple-daily-series"></a>여러 개의 일일 계열 비교

다른 상태의 하루 동안 작업은 어떻게 달라 지는가?

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| extend hour= floor( StartTime % 1d , 1h)
| where State in ("GULF OF MEXICO","MAINE","VIRGINIA","WISCONSIN","NORTH DAKOTA","NEW JERSEY","OREGON")
| summarize event_count=count() by hour, State
| render timechart
```

:::image type="content" source="images/tutorial/time-hour-state.png" alt-text="시간별 시간 차트":::

`1h`X 축을 시간 대신 시간으로 전환 하려면로 나눕니다.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| extend hour= floor( StartTime % 1d , 1h)/ 1h
| where State in ("GULF OF MEXICO","MAINE","VIRGINIA","WISCONSIN","NORTH DAKOTA","NEW JERSEY","OREGON")
| summarize event_count=count() by hour, State
| render columnchart
```

:::image type="content" source="images/tutorial/column-hour-state.png" alt-text="시간별 및 상태별 세로 막대형 차트":::

## <a name="join"></a>Join

지정 된 두 EventTypes의 상태를 확인 하는 방법

첫 번째 EventType를 사용 하 고 두 번째 EventType을 사용 하 여 스톰 이벤트를 끌어온 다음 상태에 대 한 두 집합을 조인할 수 있습니다.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| where EventType == "Lightning"
| join (
    StormEvents 
    | where EventType == "Avalanche"
) on State  
| distinct State
```

:::image type="content" source="images/tutorial/join-events-la.png" alt-text="이벤트를 라이트닝 및 아 발 조인":::

## <a name="user-session-example-of-join"></a>사용자 세션 조인의 예

이 섹션에서는 테이블을 사용 하지 않습니다 `StormEvents` .

각 사용자 세션의 시작과 끝을 표시 하는 이벤트를 포함 하는 데이터가 각 세션에 대 한 고유 ID로 가정 합니다. 

각 사용자 세션의 마지막 시간

를 사용 하 여 `extend` 두 타임 스탬프에 대 한 별칭을 제공 하면 세션 기간을 계산할 수 있습니다.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
Events
| where eventName == "session_started"
| project start_time = timestamp, stop_time, country, session_id
| join ( Events
    | where eventName == "session_ended"
    | project stop_time = timestamp, session_id
    ) on session_id
| extend duration = stop_time - start_time
| project start_time, stop_time, country, duration
| take 10
```

:::image type="content" source="images/tutorial/user-session-extend.png" alt-text="사용자 세션 확장":::

조인을 수행하기 전에 `project`을(를) 사용하여 필요한 열만 선택하는 것이 좋습니다.
동일한 절에서 타임스탬프 열의 이름을 바꿉니다.

## <a name="plot-a-distribution"></a>분포 출력

서로 다른 길이의 폭풍은 몇 개입니까?

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| extend  duration = EndTime - StartTime
| where duration > 0s
| where duration < 3h
| summarize event_count = count()
    by bin(duration, 5m)
| sort by duration asc
| render timechart
```

:::image type="content" source="images/tutorial/event-count-duration.png" alt-text="기간별 이벤트 수 시간 차트":::

또는 다음을 사용 합니다 `| render columnchart` .

:::image type="content" source="images/tutorial/column-event-count-duration.png" alt-text="기간별로 시간 차트 세로 막대형 차트 이벤트 수":::

## <a name="percentiles"></a>백분위수

다양 한 비율의 폭풍이 적용 되는 기간 범위는 어떻게 되나요?

위의 쿼리를 사용 하지만 `render` 을로 바꿉니다.

```kusto
| summarize percentiles(duration, 5, 20, 50, 80, 95)
```

이 경우 `by` 절을 제공 하지 않았으므로 결과는 단일 행입니다.

:::image type="content" source="images/tutorial/summarize-percentiles-duration.png" alt-text="기간별 백분위 수 요약":::

확인할 수 있는 사항:

* 폭풍의 5%는 5m 보다 작은 기간이 있습니다.
* 50%의 폭풍이 마지막으로 1 시간 25m 보다 작음
* 5%의 폭풍이 지난 최소 2h 50m.

각 상태에 대 한 별도의 분석을 수행 하려면 요약 연산자를 통해 상태 열을 별도로 가져오는 것이 좋습니다.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| extend  duration = EndTime - StartTime
| where duration > 0s
| where duration < 3h
| summarize event_count = count()
    by bin(duration, 5m), State
| sort by duration asc
| summarize percentiles(duration, 5, 20, 50, 80, 95) by State
```

:::image type="content" source="images/tutorial/summarize-percentiles-state.png" alt-text="상태별 백분위 수 기간 요약":::


## <a name="let-assign-a-result-to-a-variable"></a>Let: 변수에 결과 할당

위의 ' 조인 ' 예제에서 쿼리 식의 일부를 분리 하려면 [let](./letstatement.md) 을 사용 합니다. 결과는 변하지 않음:

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let LightningStorms = 
    StormEvents
    | where EventType == "Lightning";
let AvalancheStorms = 
    StormEvents
    | where EventType == "Avalanche";
LightningStorms 
| join (AvalancheStorms) on State
| distinct State
```
> [!TIP]
> Kusto 탐색기 클라이언트에서이의 파트 사이에 빈 줄을 넣지 마십시오. 이 부분을 모두 실행해야 합니다.

## <a name="combining-data-from-several-databases-in-a-query"></a>쿼리에서 여러 데이터베이스의 데이터 결합

자세한 내용은 [데이터베이스 간 쿼리](./cross-cluster-or-database-queries.md) 를 참조 하세요.

스타일의 쿼리를 작성 하는 경우:

```kusto
Logs | where ...
```

이름이 Logs 인 테이블은 기본 데이터베이스에 있어야 합니다. 다른 데이터베이스의 테이블에 액세스 하려면 다음 구문을 사용 합니다.

```kusto
database("db").Table
```

따라서 *진단* 및 *원격 분석* 이라는 데이터베이스가 있고 일부 데이터의 상관 관계를 지정 하려는 경우를 작성할 수 있습니다 ( *진단* 이 기본 데이터베이스인 것으로 가정).

```kusto
Logs | join database("Telemetry").Metrics on Request MachineId | ...
```

또는 기본 데이터베이스가 *원격 분석* 인 경우

```kusto
union Requests, database("Diagnostics").Logs | ...
```
    
위의 모든 경우에는 두 데이터베이스가 현재 연결 되어 있는 클러스터에 있는 것으로 가정 합니다. *원격 분석* 데이터베이스가 *TelemetryCluster.kusto.windows.net* 이라는 다른 클러스터에 속해 있다고 가정 하 고 액세스 하려면 다음이 필요 합니다.

```kusto
Logs | join cluster("TelemetryCluster").database("Telemetry").Metrics on Request MachineId | ...
```

> [!NOTE]
> 클러스터가 지정 된 경우 데이터베이스는 필수입니다.




::: zone-end

::: zone pivot="azuremonitor"

Kusto 쿼리 언어를 배우는 가장 좋은 방법은 몇 가지 간단한 쿼리를 확인 하 여 언어에 대 한 "느낌"을 얻는 것입니다. 이러한 쿼리는 Azure 데이터 탐색기 자습서에서 사용 되는 쿼리와 비슷하지만 Log Analytics 작업 영역에서 공통 테이블의 데이터를 사용 합니다. 

Azure Monitor의 로그 데이터를 사용 하 여 로그 쿼리를 작성 하 고 결과를 평가 하는 Azure Portal 도구인 Log Analytics를 사용 하 여 이러한 쿼리를 실행 합니다. Log Analytics에 익숙하지 않은 경우 [Log Analytics 자습서](/azure/azure-monitor/log-query/log-analytics-tutorial)에서 자습서를 진행할 수 있습니다.

모든 쿼리는 [Log Analytics 데모 환경을](https://ms.portal.azure.com/#blade/Microsoft_Azure_Monitoring_Logs/DemoLogsBlade)사용 합니다. 사용자 고유의 환경을 사용할 수 있지만 여기에 사용 된 테이블이 없을 수 있습니다. 데모 환경의 데이터가 정적이 아니기 때문에 쿼리 결과는 여기에 표시 된 결과와 약간 다를 수 있습니다.


## <a name="count-rows"></a>행 수 계산
[InsightsMetrics](/azure/azure-monitor/reference/tables/insightsmetrics) 에는 컨테이너에 대 한 VM용 Azure Monitor 및 Azure Monitor와 같은 정보로 수집 된 성능 데이터가 포함 되어 있습니다. 크기를 확인 하기 위해 해당 콘텐츠를 단순히 행 수를 계산 하는 연산자로 파이프 합니다.

쿼리는 데이터 원본 (일반적으로 테이블 이름)으로, 선택적으로 하나 이상의 파이프 문자 쌍 및 일부 테이블 형식 연산자가 올 수 있습니다. 이 경우 InsightsMetrics 테이블의 모든 레코드가 반환 된 다음 쿼리의 마지막 명령 이므로 결과를 출력 하는 [count 연산자](./countoperator.md) 연산자로 보내집니다.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
InsightsMetrics | count
```

결과:

|개수|
|-----|
|1263191|
    



## <a name="where-filtering-by-a-boolean-expression"></a>where: 부울 식으로 필터링
[Azureactivity](/azure/azure-monitor/reference/tables/azureactivity) 에는 azure에서 발생 한 구독 수준 또는 관리 그룹 수준 이벤트에 대 한 통찰력을 제공 하는 azure 활동 로그의 항목이 있습니다. `Critical`특정 주의 항목만 표시 합니다.


[Where](/azure/data-explorer/kusto/query/whereoperator) 연산자는 KQL에서 매우 일반적 이며 지정 된 조건과 일치 하는 행에 대해 테이블을 필터링 합니다. 이 예제에서는 여러 명령을 사용 합니다. 이 쿼리는 먼저 테이블의 모든 레코드를 검색 한 다음 시간 범위의 레코드에 대 한 데이터만 필터링 한 다음 수준이 인 레코드에 대해서만 결과를 필터링 합니다 `Critical` .

> [!NOTE]
> 열을 사용 하 여 쿼리에 필터를 지정 하는 것 외에도 `TimeGenerated` Log Analytics 시간 범위를 지정할 수 있습니다. 자세한 내용은 [Azure Monitor Log Analytics의 로그 쿼리 범위 및 시간 범위](/azure/azure-monitor/log-query/scope)를 참조하세요.

```kusto
AzureActivity
| where TimeGenerated > datetime(10-01-2020) and TimeGenerated < datetime(10-07-2020)
| where Level == 'Critical'
```

[![Where 필터링 예의 결과](images/tutorial/am-results-where.png)](images/tutorial/am-results-where.png#lightbox)


## <a name="project-select-a-subset-of-columns"></a>프로젝트: 열의 하위 집합 선택

[Project](./projectoperator.md) 를 사용 하 여 원하는 열만 선택 합니다. 이전 예제를 기반으로 작성 한 결과를 특정 열로 제한 하겠습니다.

```kusto
AzureActivity
| where TimeGenerated > datetime(10-01-2020) and TimeGenerated < datetime(10-07-2020)
| where Level == 'Critical'
| project TimeGenerated, Level, OperationNameValue, ResourceGroup, _ResourceId
```

[![프로젝트 예제의 결과](images/tutorial/am-results-project.png)](images/tutorial/am-results-project.png#lightbox)


## <a name="take-show-me-n-rows"></a>take: n 개 행 표시
[Networkmonitoring](/azure/azure-monitor/reference/tables/networkmonitoring) 에는 Azure virtual networks에 대 한 모니터링 데이터가 있습니다. [Take](./takeoperator.md) 연산자를 사용 하 여 해당 테이블에서 5 개의 샘플 행을 살펴보겠습니다. [Take](./takeoperator.md) 는 특정 순서로 테이블의 특정 행을 보여 줍니다.

```kusto
NetworkMonitoring
| take 10
| project TimeGenerated, Computer, SourceNetwork, DestinationNetwork, HighLatency, LowLatency
```

[![예제 사용 결과](images/tutorial/am-results-take.png)](images/tutorial/am-results-take.png#lightbox)

## <a name="sort-and-top"></a>정렬 및 위쪽
임의 레코드 대신 먼저 시간별로 정렬 하 여 최근 5 개 레코드를 반환할 수 있습니다.

```kusto
NetworkMonitoring
| sort by TimeGenerated desc
| take 5
| project TimeGenerated, Computer, SourceNetwork, DestinationNetwork, HighLatency, LowLatency
```

대신 [top](./topoperator.md) 연산자를 사용 하 여 정확한 동작을 얻을 수 있습니다. 

```kusto
NetworkMonitoring
| top 5 by TimeGenerated desc
| project TimeGenerated, Computer, SourceNetwork, DestinationNetwork, HighLatency, LowLatency
```

[![상위 예제의 결과](images/tutorial/am-results-top.png)](images/tutorial/am-results-top.png#lightbox)


## <a name="extend-compute-derived-columns"></a>확장: 파생 열 계산
[확장](./projectoperator.md) 연산자는 열을 바꾸는 대신 열 집합에 추가 한다는 점을 제외 하 고는 [프로젝트](./projectoperator.md) 와 유사 합니다. 두 연산자를 사용 하 여 각 행에 대 한 계산을 기반으로 새 열을 만들 수도 있습니다.

[Perf](/azure/azure-monitor/reference/tables/perf) 테이블은 Log Analytics 에이전트를 실행 하는 가상 머신에서 수집 된 성능 데이터를 포함 합니다. 

```kusto
Perf
| where ObjectName == "LogicalDisk" and CounterName == "Free Megabytes"
| project TimeGenerated, Computer, FreeMegabytes = CounterValue
| extend FreeGigabytes = FreeMegabytes / 1000
```

[![확장 예제의 결과](images/tutorial/am-results-extend.png)](images/tutorial/am-results-extend.png#lightbox)


## <a name="summarize-aggregate-groups-of-rows"></a>요약: 행 그룹 집계
[요약](./summarizeoperator.md) 연산자는 절에서 동일한 값을 가진 행을 그룹화 한 `by` 다음와 같은 집계 함수를 사용 `count` 하 여 각 그룹을 단일 행으로 결합 합니다. [집계 함수](./summarizeoperator.md#list-of-aggregation-functions)에는 다양 한 요약 연산자를 사용 하 여 여러 계산 열을 만들 수 있습니다. 

[Securityevent](/azure/azure-monitor/reference/tables/securityevent) 는 모니터링 되는 컴퓨터에서 시작 하는 로그온 및 프로세스와 같은 보안 이벤트를 포함 합니다. 각 컴퓨터에서 발생 한 각 수준의 이벤트 수를 계산할 수 있습니다. 이 예에서는 각 컴퓨터와 수준 조합에 대 한 행과 이벤트 수에 대 한 열이 있습니다.

```kusto
SecurityEvent
| summarize count() by Computer, Level
```

[![요약 개수 예제의 결과](images/tutorial/am-results-summarize-count.png)](images/tutorial/am-results-summarize-count.png#lightbox)


## <a name="summarize-by-scalar-values"></a>스칼라 값에 의해 요약
숫자 및 시간 값과 같은 스칼라 값으로 집계할 수 있지만 [bin ()](./binfunction.md) 함수를 사용 하 여 행을 고유 데이터 집합으로 그룹화 해야 합니다. 예를 들어를 기준으로 집계 하 `TimeGenerated` 는 경우 거의 모든 시간 값에 대 한 행을 가져옵니다. `bin()` 이러한 값을 시간 또는 일로 통합 합니다.

[InsightsMetrics](/azure/azure-monitor/reference/tables/insightsmetrics) 에는 컨테이너에 대 한 VM용 Azure Monitor 및 Azure Monitor와 같은 정보로 수집 된 성능 데이터가 포함 되어 있습니다. 다음 쿼리는 여러 컴퓨터에 대 한 시간당 평균 프로세서 사용률을 보여 줍니다.

```kusto
InsightsMetrics
| where Computer startswith "DC"
| where Namespace  == "Processor" and Name == "UtilizationPercentage"
| summarize avg(Val) by Computer, bin(TimeGenerated, 1h)
```


[![Avg 예제 요약의 결과](images/tutorial/am-results-summarize-avg.png)](images/tutorial/am-results-summarize-avg.png#lightbox)



## <a name="render-display-a-chart-or-table"></a>Render: 차트 또는 테이블을 표시 합니다.
[Render](./renderoperator.md?pivots=azuremonitor) 연산자는 쿼리의 출력을 렌더링 하는 방법을 지정 합니다. Log Analytics은 기본적으로 출력을 테이블로 렌더링 하 고 쿼리를 실행 한 후에 다른 차트 종류를 선택할 수 있습니다. `render`연산자는 특정 차트 종류를 일반적으로 선호 하는 쿼리에를 포함 하는 데 유용 합니다.

다음 예제에서는 단일 컴퓨터에 대 한 시간당 평균 프로세서 사용률을 표시 하 고 출력을 시간 차트로 렌더링 합니다.

```kusto
InsightsMetrics
| where Computer == "DC00.NA.contosohotels.com"
| where Namespace  == "Processor" and Name == "UtilizationPercentage"
| summarize avg(Val) by Computer, bin(TimeGenerated, 1h)
| render timechart
```

[![Render 예제의 결과](images/tutorial/am-results-render.png)](images/tutorial/am-results-render.png#lightbox)



## <a name="multiple-series"></a>여러 계열
절에 여러 값이 있는 경우 `summarize by` 차트에 각 값 집합에 대 한 별도의 계열이 표시 됩니다.

```kusto
InsightsMetrics
| where Computer startswith "DC"
| where Namespace  == "Processor" and Name == "UtilizationPercentage"
| summarize avg(Val) by Computer, bin(TimeGenerated, 1h)
| render timechart
```


[![여러 계열을 사용한 렌더링 결과 예](images/tutorial/am-results-render-multiple.png)](images/tutorial/am-results-render-multiple.png#lightbox)

## <a name="join-data-from-two-tables"></a>두 테이블의 데이터 조인
단일 쿼리에서 두 테이블의 데이터를 검색 해야 하는 경우는 어떻게 되나요? [Join](/azure/data-explorer/kusto/query/joinoperator?pivots=azuremonitor) 연산자를 사용 하면 여러 테이블의 행을 단일 결과 집합으로 결합할 수 있습니다. 조인에서 일치 하는 행을 인식할 수 있도록 각 테이블에 일치 하는 값을 가진 열이 있어야 합니다.

[Vmcomputer](/azure/azure-monitor/reference/tables/vmcomputer) 는 VM용 Azure Monitor에서 모니터링 하는 가상 컴퓨터에 대 한 세부 정보를 저장 하는 데 사용 되는 테이블입니다. [InsightsMetrics](/azure/azure-monitor/reference/tables/insightsmetrics) 는 해당 가상 컴퓨터에서 수집 된 성능 데이터를 저장 합니다. *InsightsMetrics* 에서 수집 된 값 중 하나는 사용 가능한 메모리 이며 사용 가능한 메모리가 백분율입니다. 백분율을 계산 하려면 *Vmcomputer* 에 있는 각 가상 컴퓨터에 대 한 실제 메모리가 필요 합니다.

다음 예제 쿼리에서는 조인을 사용 하 여이 계산을 수행 합니다. 각 컴퓨터에서 세부 정보를 정기적으로 수집 하 여 해당 테이블의 각에 대 한 여러 행을 만드는 것 이므로 [고유](/azure/data-explorer/kusto/query/joinoperator) 는 *vmcomputer* 와 함께 사용 됩니다. 두 테이블은 *컴퓨터* 열을 사용 하 여 조인 됩니다. 즉, *InsightsMetrics* 의 각 행에 대 한 두 테이블의 열을 모두 포함 하는 결과 집합에서 *vmcomputer* 의 *컴퓨터* 열 *에 있는 동일한* 값과 동일한 값을 갖는 행이 생성 됩니다.

```kusto
VMComputer
| distinct Computer, PhysicalMemoryMB
| join kind=inner (
    InsightsMetrics
    | where Namespace == "Memory" and Name == "AvailableMB"
    | project TimeGenerated, Computer, AvailableMemoryMB = Val
) on Computer
| project TimeGenerated, Computer, PercentMemory = AvailableMemoryMB / PhysicalMemoryMB * 100
```

[![조인 예제의 결과](images/tutorial/am-results-join.png)](images/tutorial/am-results-join.png#lightbox)


## <a name="let-assign-a-result-to-a-variable"></a>Let: 변수에 결과 할당
[Let](./letstatement.md) 을 사용 하 여 쿼리를 보다 쉽게 읽고 관리할 수 있습니다. 이 연산자를 사용 하면 나중에 사용할 수 있는 변수에 쿼리 결과를 할당할 수 있습니다. 이전 예제의 동일한 쿼리를 다음과 같이 다시 작성할 수 있습니다.

 
```kusto
let PhysicalComputer = VMComputer
    | distinct Computer, PhysicalMemoryMB;
    let AvailableMemory = 
InsightsMetrics
    | where Namespace == "Memory" and Name == "AvailableMB"
    | project TimeGenerated, Computer, AvailableMemoryMB = Val;
PhysicalComputer
| join kind=inner (AvailableMemory) on Computer
| project TimeGenerated, Computer, PercentMemory = AvailableMemoryMB / PhysicalMemoryMB * 100
```

[![Let 예제의 결과](images/tutorial/am-results-let.png)](images/tutorial/am-results-let.png#lightbox)

## <a name="next-steps"></a>다음 단계

- [Kusto 쿼리 언어에 대 한 코드 샘플을 봅니다](samples.md?pivots=azuremonitor).


::: zone-end
