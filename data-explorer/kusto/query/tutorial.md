---
title: '자습서: Azure Data Explorer 및 Azure Monitor의 Kusto 쿼리'
description: 이 자습서에서는 Kusto 쿼리 언어의 쿼리를 사용하여 Azure Data Explorer 및 Azure Monitor의 일반적인 쿼리 요구 사항을 충족하는 방법을 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/08/2020
ms.localizationpriority: high
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 0616c0d4dd2a44ba1d54ac83c699a1c0bac249b0
ms.sourcegitcommit: f134d51e52504d3ca722bdf6d33baee05118173a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/03/2020
ms.locfileid: "96563362"
---
# <a name="tutorial-use-kusto-queries-in-azure-data-explorer-and-azure-monitor"></a>자습서: Azure Data Explorer 및 Azure Monitor에서 Kusto 쿼리 사용

::: zone pivot="azuredataexplorer"

Kusto 쿼리 언어를 배우는 가장 좋은 방법은 몇 가지 기본 쿼리를 살펴보면서 언어에 대한 "느낌"을 찾는 것입니다. [몇 가지 샘플 데이터가 포함된 데이터베이스](https://help.kusto.windows.net/Samples)를 사용하는 것이 좋습니다. 이 자습서에서 설명하는 쿼리는 이 데이터베이스에서 실행해야 합니다. 샘플 데이터베이스의 `StormEvents` 테이블은 미국에서 발생한 폭풍에 대한 정보를 제공합니다.

## <a name="count-rows"></a>행 수 계산

예제 데이터베이스에는 `StormEvents`라는 테이블이 있습니다. 테이블 크기를 알아내기 위해, 간단하게 테이블의 행 수를 세는 연산자에 테이블의 내용을 파이핑할 것입니다. 

*구문 메모*: 쿼리는 데이터 원본(일반적으로 테이블 이름)이며, 선택 사항으로 파이프 문자와 테이블 형식 연산자로 이루어진 하나 이상의 쌍이 쿼리 뒤에 올 수 있습니다.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents | count
```

출력은 다음과 같습니다.

|개수|
|-----|
|59066|
    
자세한 내용은 [count 연산자](./countoperator.md)를 참조하세요.

## <a name="select-a-subset-of-columns-project"></a>열 하위 집합 선택: *project*

[project](./projectoperator.md)를 사용하여 원하는 열만 선택합니다. [project](./projectoperator.md) 및 [take](./takeoperator.md) 연산자를 사용하는 다음 예제를 참조하세요.

## <a name="filter-by-boolean-expression-where"></a>부울 식으로 필터링: *where*

2007년 2월에 `California`에서 발생한 `flood` 이벤트만 표시 해 보겠습니다.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| where StartTime > datetime(2007-02-01) and StartTime < datetime(2007-03-01)
| where EventType == 'Flood' and State == 'CALIFORNIA'
| project StartTime, EndTime , State , EventType , EpisodeNarrative
```

출력은 다음과 같습니다.

|StartTime|EndTime|시스템 상태|EventType|EpisodeNarrative|
|---|---|---|---|---|
|2007-02-19 00:00:00.0000000|2007-02-19 08:00:00.0000000|캘리포니아|홍수|서던 샌 와킨 밸리를 관통하는 전선계가 19일 이른 아침 시간에 짧은 시간 동안 컨 카운티에 폭우를 쏟았습니다. 태프트 인근 166번 고속도로에서 가벼운 홍수가 발생했다고 보고되었습니다.|

## <a name="show-n-rows-take"></a>*n* 개 행 표시: *take*

데이터를 살펴보겠습니다. 무작위 샘플 5개 행에 무엇이 들어 있을까요?

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| take 5
| project  StartTime, EndTime, EventType, State, EventNarrative  
```

출력은 다음과 같습니다.

|StartTime|EndTime|EventType|시스템 상태|EventNarrative|
|---|---|---|---|---|
|2007-09-18 20:00:00.0000000|2007-09-19 18:00:00.0000000|폭우|FLORIDA|해안에 위치한 볼루시아 카운티 곳곳에서 22.8센치미터의 비가 쏟아졌습니다.|
|2007-09-20 21:57:00.0000000|2007-09-20 22:05:00.0000000|토네이도|FLORIDA|웨스트 크루키드 레이크의 북쪽 끝에 위치한 유스티스 타운을 토네이도가 덮쳤습니다. 유스티스를 관통하여 북북서 방향으로 이동한 이 토네이도는 빠르게 EF1 등급으로 강해졌습니다. 이동 거리는 3.2킬로미터가 채 되지 않았으며 최대 너비는 274미터였습니다.  이 토네이도로 인해 가옥 7채가 파괴되었습니다. 가옥 27채가 심각하게 손상되고 가옥 81채가 약간 손상되었다고 보고되었습니다. 심각한 부상자는 없었으며 재산 피해는 620만 달러로 집계되었습니다.|
|2007-09-29 08:11:00.0000000|2007-09-29 08:11:00.0000000|용오름|대서양 남부|멜번 비치 대서양 남동쪽에서 형성된 용오름이 해안을 향해 짧게 이동했습니다.|
|2007-12-20 07:50:00.0000000|2007-12-20 07:53:00.0000000|뇌우를 동반한 바람|미시시피|수많은 대형 나무가 강풍에 쓰러졌으며 그 중 일부는 송전선 위로 넘어졌습니다. 애덤스 카운티 동부에서 피해가 발생했습니다.|
|2007-12-30 16:00:00.0000000|2007-12-30 16:05:00.0000000|뇌우를 동반한 바람|GEORGIA|카운티 당국의 발표에 따르면 206번 고속도로 인근의 퀸시 배튼 루프를 따라 대형 나무 여러 그루가 강풍에 쓰러졌습니다. 나무 제거 비용 견적이 산출되었습니다.|

하지만 [take](./takeoperator.md)는 테이블의 행을 특정 순서로 표시하지 않습니다. 그래서 이번에는 행을 정렬해 보겠습니다. [limit](./takeoperator.md)는 [take](./takeoperator.md)의 별칭이며 효과가 동일합니다.

## <a name="order-results-sort-top"></a>결과 정렬: *sort*, *top*

* *구문 메모*: 일부 연산자는 `by`와 같은 키워드를 통해 적용되는 매개 변수를 갖고 있습니다.
* 다음 예제에서 `desc`는 결과를 내림차순으로 정렬하고 `asc`는 결과를 오름차순으로 정렬합니다.

다음과 같이 처음 *n* 개 행을 특정 열 기준으로 정렬하여 보여줍니다.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| top 5 by StartTime desc
| project  StartTime, EndTime, EventType, State, EventNarrative  
```

출력은 다음과 같습니다.

|StartTime|EndTime|EventType|시스템 상태|EventNarrative|
|---|---|---|---|---|
|2007-12-31 22:30:00.0000000|2007-12-31 23:59:00.0000000|겨울 폭풍|미시간|새해 첫날 오전에도 이 폭설이 계속 이어졌습니다.|
|2007-12-31 22:30:00.0000000|2007-12-31 23:59:00.0000000|겨울 폭풍|미시간|새해 첫날 오전에도 이 폭설이 계속 이어졌습니다.|
|2007-12-31 22:30:00.0000000|2007-12-31 23:59:00.0000000|겨울 폭풍|미시간|새해 첫날 오전에도 이 폭설이 계속 이어졌습니다.|
|2007-12-31 23:53:00.0000000|2007-12-31 23:53:00.0000000|강풍|캘리포니아|벤투라 카운티 산맥 북쪽에서 북동쪽으로 시속 93km의 강풍이 분다고 보고되었습니다.|
|2007-12-31 23:53:00.0000000|2007-12-31 23:53:00.0000000|강풍|캘리포니아|시속 93km의 강력한 편북풍이 분다고 Warm Springs RAWS 센서에서 보고했습니다.|

[sort](./sortoperator.md)를 사용한 다음, [take](./takeoperator.md)를 사용하여 동일한 결과를 얻을 수 있습니다.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| sort by StartTime desc
| take 5
| project  StartTime, EndTime, EventType, EventNarrative
```

## <a name="compute-derived-columns-extend"></a>파생된 열 컴퓨팅: *extend*

다음과 같이 모든 행의 값을 컴퓨팅하여 새 열을 만듭니다.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| limit 5
| extend Duration = EndTime - StartTime 
| project StartTime, EndTime, Duration, EventType, State
```

출력은 다음과 같습니다.

|StartTime|EndTime|기간|EventType|시스템 상태|
|---|---|---|---|---|
|2007-09-18 20:00:00.0000000|2007-09-19 18:00:00.0000000|22:00:00|폭우|FLORIDA|
|2007-09-20 21:57:00.0000000|2007-09-20 22:05:00.0000000|00:08:00|토네이도|FLORIDA|
|2007-09-29 08:11:00.0000000|2007-09-29 08:11:00.0000000|00:00:00|용오름|대서양 남부|
|2007-12-20 07:50:00.0000000|2007-12-20 07:53:00.0000000|00:03:00|뇌우를 동반한 바람|미시시피|
|2007-12-30 16:00:00.0000000|2007-12-30 16:05:00.0000000|00:05:00|뇌우를 동반한 바람|GEORGIA|

열 이름을 다시 사용하고 계산 결과를 동일한 열에 할당할 수 있습니다.

예:

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print x=1
| extend x = x + 1, y = x
| extend x = x + 1
```

출력은 다음과 같습니다.

|x|y|
|---|---|
|3|1|

[스칼라 식](./scalar-data-types/index.md)은 일반적인 연산자(`+`, `-`, `*`, `/`, `%`)를 모두 포함할 수 있으며, 여러 가지 유용한 함수가 제공됩니다.

## <a name="aggregate-groups-of-rows-summarize"></a>행 그룹 집계: *summarize*

다음과 같이 각 국가에서 발생하는 이벤트 수를 계산합니다.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| summarize event_count = count() by State
```

[summarize](./summarizeoperator.md) 연산자는 `by` 절의 값이 같은 행을 그룹화한 다음, 집계 함수(예: `count`)를 사용하여 각 그룹을 단일 행으로 결합합니다. 이 예제의 경우 각 주에 해당하는 행이 있고 그 주의 행 개수에 해당하는 열이 있습니다.

다양한 [집계 함수](./summarizeoperator.md#list-of-aggregation-functions)가 제공됩니다. 여러 집계 함수를 하나의 `summarize` 연산자에 사용하여 여러 계산 열을 생성할 수 있습니다. 예를 들어 각 주에서 발생한 폭풍 수와 각 주의 고유한 폭풍 유형 합계를 얻을 수 있습니다. 그런 다음, [top](./topoperator.md)을 사용하여 폭풍의 영향을 가장 많이 받은 주를 가져올 수 있습니다.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents 
| summarize StormCount = count(), TypeOfStorms = dcount(EventType) by State
| top 5 by StormCount desc
```

출력은 다음과 같습니다.

|시스템 상태|StormCount|TypeOfStorms|
|---|---|---|
|텍사스|4701|27|
|캔자스|3166|21|
|아이오와|2337|19|
|일리노이|2022|23|
|미주리|2016|20|

`summarize` 연산자의 결과에서:

* 각 열 이름은 `by`로 지정됩니다.
* 계산 식마다 열이 있습니다.
* `by` 값 조합마다 행이 있습니다.

## <a name="summarize-by-scalar-values"></a>스칼라 값에 의해 요약

`by` 절에 스칼라(숫자, 시간 또는 간격) 값을 사용할 수 있지만, [bin()](./binfunction.md) 함수를 사용하여 값을 bin에 포함하는 것이 좋습니다.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| where StartTime > datetime(2007-02-14) and StartTime < datetime(2007-02-21)
| summarize event_count = count() by bin(StartTime, 1d)
```

이 쿼리는 모든 타임스탬프를 하루 간격으로 줄입니다.

|StartTime|event_count|
|---|---|
|2007-02-14 00:00:00.0000000|180|
|2007-02-15 00:00:00.0000000|66|
|2007-02-16 00:00:00.0000000|164|
|2007-02-17 00:00:00.0000000|103|
|2007-02-18 00:00:00.0000000|22|
|2007-02-19 00:00:00.0000000|52|
|2007-02-20 00:00:00.0000000|60|

[bin()](./binfunction.md)은 여러 언어에서 [floor()](./floorfunction.md) 함수와 동일합니다. 모든 값을 사용자가 제공하는 모듈러스의 가장 근접한 배수로 줄이므로, [summarize](./summarizeoperator.md)가 그룹에 행을 할당할 수 있습니다.

<a name="displaychartortable"></a>
## <a name="display-a-chart-or-table-render"></a>차트 또는 테이블 표시: *render*

다음과 같이 두 열을 프로젝션하여 차트의 x-축과 y-축으로 사용할 수 있습니다.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents 
| summarize event_count=count(), mid = avg(BeginLat) by State 
| sort by mid
| where event_count > 1800
| project State, event_count
| render columnchart
```

:::image type="content" source="images/tutorial/event-counts-state.png" alt-text="각 주의 폭풍 이벤트 수를 세로 막대형 차트로 보여주는 스크린샷":::

`project` 작업에서 `mid`를 제거했지만, 차트에서 해당 순서로 국가를 표시하려면 여전히 mid가 필요합니다.

엄밀하게 말해서, `render`는 쿼리 언어의 일부가 아니라 클라이언트의 기능입니다. 하지만 언어에 통합되어 있으며, 결과를 시각적으로 표시하는 유용합니다.


## <a name="timecharts"></a>시간 차트

숫자 bin으로 돌아가서, 시계열을 표시하겠습니다.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| summarize event_count=count() by bin(StartTime, 1d)
| render timechart
```

:::image type="content" source="images/tutorial/time-series-start-bin.png" alt-text="시간 bin으로 저장된 이벤트의 꺾은선형 차트 스크린샷":::

## <a name="multiple-series"></a>여러 계열

`summarize by` 절에 여러 값을 사용하여 각 값 조합에 대해 별도의 행을 생성합니다.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents 
| where StartTime > datetime(2007-06-04) and StartTime < datetime(2007-06-10) 
| where Source in ("Source","Public","Emergency Manager","Trained Spotter","Law Enforcement")
| summarize count() by bin(StartTime, 10h), Source
```

:::image type="content" source="images/tutorial/table-count-source.png" alt-text="출처별 수를 표로 보여주는 스크린샷":::

간단하게 이전 예제 `| render timechart` 앞에 `render` 용어를 추가합니다.

:::image type="content" source="images/tutorial/line-count-source.png" alt-text="출처별 수를 꺾은선형 차트로 보여주는 스크린샷":::

`render timechart`는 첫 번째 열을 x-축으로 사용하고, 나머지 열을 별도의 선으로 표시합니다.

## <a name="daily-average-cycle"></a>일일 평균 주기

평균적으로 하루 동안 활동이 어떻게 변할까요?

시간 모듈로 하루 동안 발생하는 이벤트 수를 집계하여 시간 bin으로 저장합니다. 여기서는 `bin` 대신 `floor`를 사용합니다.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| extend hour = floor(StartTime % 1d , 1h)
| summarize event_count=count() by hour
| sort by hour asc
| render timechart
```

:::image type="content" source="images/tutorial/time-count-hour.png" alt-text="출처별 수를 시간 차트로 보여주는 스크린샷":::

현재 `render`는 기간에 적절한 레이블을 지정하지 않으며, 그 대신 다음과 같이 `| render columnchart`를 사용할 수 있습니다.

:::image type="content" source="images/tutorial/column-count-hour.png" alt-text="출처별 수를 세로 막대형 차트로 보여주는 스크린샷":::

## <a name="compare-multiple-daily-series"></a>여러 개의 일일 계열 비교

각 주에서 시간에 따라 활동이 어떻게 변할까요?

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| extend hour= floor( StartTime % 1d , 1h)
| where State in ("GULF OF MEXICO","MAINE","VIRGINIA","WISCONSIN","NORTH DAKOTA","NEW JERSEY","OREGON")
| summarize event_count=count() by hour, State
| render timechart
```

:::image type="content" source="images/tutorial/time-hour-state.png" alt-text="시간과 주에 따른 변화를 보여주는 시간 차트의 스크린샷":::

`1h`로 나눠서 x-축을 기간 대신 시간을 나타내는 숫자로 바꿉니다.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| extend hour= floor( StartTime % 1d , 1h)/ 1h
| where State in ("GULF OF MEXICO","MAINE","VIRGINIA","WISCONSIN","NORTH DAKOTA","NEW JERSEY","OREGON")
| summarize event_count=count() by hour, State
| render columnchart
```

:::image type="content" source="images/tutorial/column-hour-state.png" alt-text="시간과 주에 따른 변화를 보여주는 세로 막대형 차트의 스크린샷":::

## <a name="join-data-types"></a>데이터 형식 조인

두 가지 특정 이벤트 유형을 찾고, 각 이벤트가 어떤 주에서 발생했는지 알아보려면 어떻게 할까요?

첫 번째 `EventType`과 두 번째 `EventType`을 사용하여 폭풍 이벤트를 끌어온 다음, 두 세트를 `State`에 조인할 수 있습니다.

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

:::image type="content" source="images/tutorial/join-events-lightning-avalanche.png" alt-text="번개 이벤트와 눈(산)사태 이벤트의 조인을 보여주는 스크린샷":::

## <a name="user-session-example-of-join"></a>*join* 의 사용자 세션 예제

이 섹션에서는 `StormEvents` 테이블을 사용하지 않습니다.

어떤 데이터가 있는데, 이 데이터 안에는 각 사용자 세션의 시작과 끝을 각 세션의 고유 ID로 표시하는 이벤트가 들어 있다고 가정해 보겠습니다. 

각 사용자 세션이 얼마나 지속되는지 어떻게 알 수 있을까요?

다음과 같이 `extend`를 사용하여 두 타임스탬프의 별칭을 제공한 다음, 세션 기간을 컴퓨팅하면 됩니다.

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

:::image type="content" source="images/tutorial/user-session-extend.png" alt-text="사용자 세션 범위의 결과 표 스크린샷":::

조인을 수행하기 전에 `project`를 사용하여 필요한 열만 선택하는 것이 좋습니다. 동일한 절에서 `timestamp` 열의 이름을 바꿉니다.

## <a name="plot-a-distribution"></a>분포 출력

`StormEvents` 테이블로 돌아가서, 지속 시간이 다른 폭풍은 몇 개입니까?

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

:::image type="content" source="images/tutorial/event-count-duration.png" alt-text="지속 시간별 이벤트 수 결과를 보여주는 시간 차트의 스크린샷":::

또는 다음과 같이 `| render columnchart`를 사용해도 됩니다.

:::image type="content" source="images/tutorial/column-event-count-duration.png" alt-text="지속 시간별 이벤트 수 결과를 보여주는 세로 막대형 차트의 스크린샷":::

## <a name="percentiles"></a>백분위수

폭풍의 지속 시간에 따른 백분위수는 어떻게 될까요?

이 정보를 얻으려면 이전 쿼리를 사용하되, `render`를 다음과 같이 바꿉니다.

```kusto
| summarize percentiles(duration, 5, 20, 50, 80, 95)
```

이 예제에서는 `by` 절을 사용하지 않았으므로 출력이 단일 행입니다.

:::image type="content" source="images/tutorial/summarize-percentiles-duration.png" lightbox="images/tutorial/summarize-percentiles-duration.png" alt-text="지속 시간에 따른 백분위수 요약 결과를 보여주는 표의 스크린샷":::

출력을 보면 다음과 같은 사실을 확인할 수 있습니다.

* 폭풍의 5%는 지속 시간이 5분 미만입니다.
* 폭풍의 50%는 지속 시간이 1시간 25분 미만입니다.
* 폭풍의 5%는 2시간 50분 이상 지속되었습니다.

정보를 주별로 분류하려면 다음과 같이 `state` 열을 두 `summarize` 연산자에 별도로 사용합니다.

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

:::image type="content" source="images/tutorial/summarize-percentiles-state.png" alt-text="각 주의 지속 시간에 따른 백분위수를 요약하여 보여주는 표":::

## <a name="assign-a-result-to-a-variable-let"></a>결과를 변수에 할당: *let*

[let](./letstatement.md)을 사용하여 이전 `join` 예제에서 쿼리 식의 일부를 분리할 수 있습니다. 결과는 변하지 않음:

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
> Kusto 탐색기에서 전체 쿼리를 실행하려면 쿼리 사이에 빈 줄을 추가하지 마세요.

## <a name="combine-data-from-several-databases-in-a-query"></a>여러 데이터베이스의 데이터를 쿼리 하나에 결합

다음 쿼리에서 `Logs` 테이블은 기본 데이터베이스에 있어야 합니다.

```kusto
Logs | where ...
```

다른 데이터베이스의 테이블에 액세스하려면 다음 구문을 사용합니다.

```kusto
database("db").Table
```

예를 들어 `Diagnostics` 및 `Telemetry`라는 데이터베이스가 있고 두 테이블에 들어 있는 데이터 간의 상관 관계를 지정하려는 경우 다음 쿼리를 사용할 수 있습니다(`Diagnostics`가 기본 데이터베이스라고 가정).

```kusto
Logs | join database("Telemetry").Metrics on Request MachineId | ...
```

기본 데이터베이스가 `Telemetry`인 경우 다음 쿼리를 사용합니다.

```kusto
union Requests, database("Diagnostics").Logs | ...
```
    
위의 두 쿼리에서는 두 데이터베이스가 현재 연결된 클러스터에 있다고 가정합니다. `Telemetry` 데이터베이스가 *TelemetryCluster.kusto.windows.net* 이라는 클러스터에 있었다면 다음 쿼리를 사용하여 액세스합니다.

```kusto
Logs | join cluster("TelemetryCluster").database("Telemetry").Metrics on Request MachineId | ...
```

> [!NOTE]
> 클러스터를 지정하면 데이터베이스가 필수입니다.

여러 데이터베이스의 데이터를 쿼리 하나에 결합하는 방법에 대한 자세한 내용은 [데이터베이스 간 쿼리](cross-cluster-or-database-queries.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

- [Kusto 쿼리 언어](samples.md?pivots=azuredataexplorer)에 대한 코드 샘플을 살펴봅니다.

::: zone-end

::: zone pivot="azuremonitor"

Kusto 쿼리 언어를 배우는 가장 좋은 방법은 몇 가지 기본 쿼리를 살펴보면서 언어에 대한 "느낌"을 찾는 것입니다. 이러한 쿼리는 Azure Data Explorer 자습서에서 사용되는 쿼리와 비슷하지만, 공용 테이블의 데이터를 Azure Log Analytics 작업 영역에서 사용합니다. 

Azure Portal에서 Log Analytics를 사용하여 이러한 쿼리를 실행합니다. Log Analytics는 로그 쿼리를 작성하는 데 사용할 수 있는 도구입니다. Azure Monitor에서 로그 데이터를 사용한 다음, 로그 쿼리 결과를 평가합니다. Log Analytics에 익숙하지 않은 경우 [Log Analytics 자습서](/azure/azure-monitor/log-query/log-analytics-tutorial)를 완료합니다.

이 자습서의 모든 쿼리는 [Log Analytics 데모 환경](https://ms.portal.azure.com/#blade/Microsoft_Azure_Monitoring_Logs/DemoLogsBlade)을 사용합니다. 사용자 고유의 환경을 사용해도 되지만, 이 자습서에 사용되는 테이블 중 일부가 없을 수도 있습니다. 데모 환경의 데이터는 정적이지 않기 때문에 쿼리 결과는 여기에 표시된 결과와 약간 다를 수 있습니다.


## <a name="count-rows"></a>행 수 계산

[InsightsMetrics](/azure/azure-monitor/reference/tables/insightsmetrics) 테이블에는 VM용 Azure Monitor 및 컨테이너용 Azure Monitor 같은 인사이트 기능을 통해 수집되는 성능 데이터가 포함되어 있습니다. 테이블 크기를 알아내기 위해, 간단하게 행 수를 세는 연산자에 테이블의 내용을 파이핑할 것입니다.

쿼리는 데이터 원본(일반적으로 테이블 이름)이며, 선택 사항으로 파이프 문자와 테이블 형식 연산자로 이루어진 하나 이상의 쌍이 쿼리 뒤에 올 수 있습니다. 이 예제에서는 `InsightsMetrics` 테이블의 모든 레코드가 반환된 다음, [count 연산자](./countoperator.md)로 전송됩니다. `count` 연산자는 쿼리의 마지막 명령이므로 결과를 표시합니다.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
InsightsMetrics | count
```

출력은 다음과 같습니다.

|개수|
|-----|
|1,263,191|
    

## <a name="filter-by-boolean-expression-where"></a>부울 식으로 필터링: *where*

[AzureActivity](/azure/azure-monitor/reference/tables/azureactivity) 테이블에는 Azure에서 발생한 구독 수준 또는 관리 그룹 수준 이벤트에 대한 인사이트를 제공하는 Azure 활동 로그의 항목이 들어 있습니다. 특정 주에 발생한 `Critical` 항목만 보도록 하겠습니다.

[where](/azure/data-explorer/kusto/query/whereoperator) 연산자는 Kusto 쿼리 언어에서 공통적으로 사용됩니다. `where`는 테이블을 특정 조건과 일치하는 행으로 필터링합니다. 다음 예제에서는 여러 명령을 사용합니다. 먼저 쿼리는 테이블의 모든 레코드를 검색합니다. 그런 다음, 데이터를 필터링하여 시간 범위에 있는 레코드만 찾습니다. 마지막으로 결과를 필터링하여 `Critical` 수준의 레코드만 찾습니다.

> [!NOTE]
> `TimeGenerated` 열을 사용하여 쿼리에서 필터를 지정할 수 있을 뿐 아니라, Log Analytics에서 시간 범위를 지정할 수 있습니다. 자세한 내용은 [Azure Monitor Log Analytics의 로그 쿼리 범위 및 시간 범위](/azure/azure-monitor/log-query/scope)를 참조하세요.

```kusto
AzureActivity
| where TimeGenerated > datetime(10-01-2020) and TimeGenerated < datetime(10-07-2020)
| where Level == 'Critical'
```

:::image type="content" source="images/tutorial/azure-monitor-where-results.png" lightbox="images/tutorial/azure-monitor-where-results.png" alt-text="where 연산자 예제의 결과를 보여주는 스크린샷":::

## <a name="select-a-subset-of-columns-project"></a>열 하위 집합 선택: *project*

[project](./projectoperator.md)를 사용하여 원하는 열만 포함합니다. 이전 예제를 계속 진행하여, 출력을 특정 열로 제한해 보겠습니다.

```kusto
AzureActivity
| where TimeGenerated > datetime(10-01-2020) and TimeGenerated < datetime(10-07-2020)
| where Level == 'Critical'
| project TimeGenerated, Level, OperationNameValue, ResourceGroup, _ResourceId
```

:::image type="content" source="images/tutorial/azure-monitor-project-results.png" lightbox="images/tutorial/azure-monitor-project-results.png" alt-text="project 연산자 예제의 결과를 보여주는 스크린샷":::

## <a name="show-n-rows-take"></a>*n* 개 행 표시: *take*

[NetworkMonitoring](/azure/azure-monitor/reference/tables/networkmonitoring)은 Azure 가상 네트워크의 모니터링 데이터를 포함합니다. [take](./takeoperator.md) 연산자를 사용하여 이 테이블에 들어 있는 10개 샘플 행을 무작위로 살펴보겠습니다. [take](./takeoperator.md)는 테이블에 들어 있는 특정 개수의 행을 특별한 순서 없이 표시합니다.

```kusto
NetworkMonitoring
| take 10
| project TimeGenerated, Computer, SourceNetwork, DestinationNetwork, HighLatency, LowLatency
```

:::image type="content" source="images/tutorial/azure-monitor-take-results.png" lightbox="images/tutorial/azure-monitor-take-results.png" alt-text="take 연산자 예제의 결과를 보여주는 스크린샷":::

## <a name="order-results-sort-top"></a>결과 정렬: *sort*, *top*

임의의 레코드 대신, 시간순으로 정렬하여 가장 최근의 레코드 5개를 반환할 수 있습니다.

```kusto
NetworkMonitoring
| sort by TimeGenerated desc
| take 5
| project TimeGenerated, Computer, SourceNetwork, DestinationNetwork, HighLatency, LowLatency
```

[top](./topoperator.md) 연산자를 사용하면 이 동작을 정확하게 수행할 수 있습니다. 

```kusto
NetworkMonitoring
| top 5 by TimeGenerated desc
| project TimeGenerated, Computer, SourceNetwork, DestinationNetwork, HighLatency, LowLatency
```

:::image type="content" source="images/tutorial/azure-monitor-top-results.png" lightbox="images/tutorial/azure-monitor-top-results.png" alt-text="top 연산자 예제의 결과를 보여주는 스크린샷":::

## <a name="compute-derived-columns-extend"></a>파생된 열 컴퓨팅: *extend*

[extend](./projectoperator.md) 연산자는 [project](./projectoperator.md) 연산자와 비슷하지만, 열을 바꾸는 대신 열 세트에 추가합니다. 두 연산자를 사용하여 각 행의 계산 결과에 따라 새 열을 만들 수 있습니다.

[Perf](/azure/azure-monitor/reference/tables/perf) 테이블에는 Log Analytics 에이전트를 실행하는 가상 머신에서 수집하는 성능 데이터가 들어 있습니다. 

```kusto
Perf
| where ObjectName == "LogicalDisk" and CounterName == "Free Megabytes"
| project TimeGenerated, Computer, FreeMegabytes = CounterValue
| extend FreeGigabytes = FreeMegabytes / 1000
```

:::image type="content" source="images/tutorial/azure-monitor-extend-results.png" lightbox="images/tutorial/azure-monitor-extend-results.png" alt-text="extend 연산자 예제의 결과를 보여주는 스크린샷":::

## <a name="aggregate-groups-of-rows-summarize"></a>행 그룹 집계: *summarize*

[summarize](./summarizeoperator.md) 연산자는 `by` 절의 값이 같은 행을 그룹화합니다. 그런 다음, `count` 같은 집계 함수를 사용하여 각 그룹을 단일 행에 결합합니다. 다양한 [집계 함수](./summarizeoperator.md#list-of-aggregation-functions)가 제공됩니다. 여러 집계 함수를 하나의 `summarize` 연산자에 사용하여 여러 계산 열을 생성할 수 있습니다. 

[SecurityEvent](/azure/azure-monitor/reference/tables/securityevent) 테이블에는 모니터링되는 컴퓨터에서 시작된 로그온 및 프로세스와 같은 보안 이벤트가 들어 있습니다. 각 수준의 이벤트가 각 컴퓨터에서 몇 건이나 발생했는지 집계할 수 있습니다. 이 예제에서는 컴퓨터와 수준의 조합마다 행이 하나씩 생성됩니다. 열에는 이벤트 수가 포함됩니다.

```kusto
SecurityEvent
| summarize count() by Computer, Level
```

:::image type="content" source="images/tutorial/azure-monitor-summarize-count-results.png" lightbox="images/tutorial/azure-monitor-summarize-count-results.png" alt-text="summarize count 연산자 예제의 결과를 보여주는 스크린샷":::

## <a name="summarize-by-scalar-values"></a>스칼라 값에 의해 요약

숫자 및 시간 값과 같은 스칼라 값으로 집계할 수 있지만, [bin()](./binfunction.md) 함수를 사용하여 행을 별도의 데이터 세트로 그룹화해야 합니다. 예를 들어 `TimeGenerated`로 집계하는 경우 거의 모든 시간 값마다 행이 있습니다. `bin()`을 사용하여 이러한 값을 시간 또는 일로 통합할 수 있습니다.

[InsightsMetrics](/azure/azure-monitor/reference/tables/insightsmetrics) 테이블에는 VM용 Azure Monitor 및 컨테이너용 Azure Monitor 같은 인사이트 기능을 통해 수집되는 성능 데이터가 포함되어 있습니다. 다음 쿼리는 여러 컴퓨터의 시간당 평균 프로세서 사용률을 보여줍니다.

```kusto
InsightsMetrics
| where Computer startswith "DC"
| where Namespace  == "Processor" and Name == "UtilizationPercentage"
| summarize avg(Val) by Computer, bin(TimeGenerated, 1h)
```

:::image type="content" source="images/tutorial/azure-monitor-summarize-avg-results.png" lightbox="images/tutorial/azure-monitor-summarize-avg-results.png" alt-text="avg 연산자 예제의 결과를 보여주는 스크린샷":::

## <a name="display-a-chart-or-table-render"></a>차트 또는 테이블 표시: *render*

[render](./renderoperator.md?pivots=azuremonitor) 연산자는 쿼리의 출력을 렌더링하는 방법을 지정합니다. Log Analytics는 기본적으로 출력을 테이블로 렌더링합니다. 쿼리를 실행한 후 다양한 차트 종류를 선택할 수 있습니다. `render` 연산자는 일반적으로 특정 차트 종류가 많이 사용되는 쿼리에서 유용합니다.

다음 예제는 단일 컴퓨터의 시간당 평균 프로세서 사용률을 보여줍니다. 이 예제에서는 출력을 시간 차트로 렌더링합니다.

```kusto
InsightsMetrics
| where Computer == "DC00.NA.contosohotels.com"
| where Namespace  == "Processor" and Name == "UtilizationPercentage"
| summarize avg(Val) by Computer, bin(TimeGenerated, 1h)
| render timechart
```

:::image type="content" source="images/tutorial/azure-monitor-render-results.png" lightbox="images/tutorial/azure-monitor-render-results.png" alt-text="render 연산자 예제의 결과를 보여주는 스크린샷":::

## <a name="work-with-multiple-series"></a>여러 계열 사용

`summarize by` 절에 여러 값을 사용하는 경우 각각의 값 세트마다 별도의 계열이 차트에 표시됩니다.

```kusto
InsightsMetrics
| where Computer startswith "DC"
| where Namespace  == "Processor" and Name == "UtilizationPercentage"
| summarize avg(Val) by Computer, bin(TimeGenerated, 1h)
| render timechart
```

:::image type="content" source="images/tutorial/azure-monitor-render-multiple-results.png" lightbox="images/tutorial/azure-monitor-render-multiple-results.png" alt-text="여러 계열을 사용하는 render 연산자 예제의 결과를 보여주는 스크린샷":::

## <a name="join-data-from-two-tables"></a>두 테이블의 데이터 조인

단일 쿼리에서 두 테이블의 데이터를 검색해야 하는 경우에는 어떻게 할까요? [join](/azure/data-explorer/kusto/query/joinoperator?pivots=azuremonitor) 연산자를 사용하면 여러 테이블의 행을 단일 결과 세트에 결합할 수 있습니다. 매칭할 행을 join 연산자가 인식할 수 있도록 각 테이블에는 일치하는 값을 가진 열이 있어야 합니다.

[VMComputer](/azure/azure-monitor/reference/tables/vmcomputer)는 Azure Monitor가 모니터링하는 VM에 대한 세부 정보를 저장하기 위해 가상 머신에 사용하는 테이블입니다. [InsightsMetrics](/azure/azure-monitor/reference/tables/insightsmetrics)는 이러한 가상 머신에서 수집한 성능 데이터가 들어 있습니다. *InsightsMetrics* 에서 수집한 값 하나는 사용 가능한 메모리이지만, 사용 가능한 백분율 메모리는 아닙니다. 백분율을 계산하려면 각 가상 머신의 실제 메모리가 필요합니다. 이 값은 `VMComputer`에 있습니다.

다음 예제 쿼리에서는 join을 사용하여 이 계산을 수행합니다. 각 컴퓨터에서 세부 정보를 정기적으로 수집하기 때문에 [distinct](/azure/data-explorer/kusto/query/distinctoperator) 연산자는 `VMComputer`와 함께 사용됩니다. 따라서 테이블의 컴퓨터마다 여러 행이 생성됩니다. `Computer` 열을 사용하여 두 테이블을 조인합니다. 결과 세트에 행이 하나 생성되고, 이 행에는 `InsightsMetrics`의 각 행에 대한 두 테이블의 열이 포함됩니다. `Computer`의 값은 `VMComputer`에 있는 `Computer` 열의 값과 일치합니다.

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

:::image type="content" source="images/tutorial/azure-monitor-join-results.png" lightbox="images/tutorial/azure-monitor-join-results.png" alt-text="join 연산자 예제의 결과를 보여주는 스크린샷":::

## <a name="assign-a-result-to-a-variable-let"></a>결과를 변수에 할당: *let*

[let](./letstatement.md)을 사용하면 쿼리를 보다 쉽게 읽고 관리할 수 있습니다. 이 연산자를 사용하여 쿼리 결과를 변수에 할당하고 나중에 사용할 수 있습니다. `let` 문을 사용하면 이전 예제의 쿼리를 다음과 같이 다시 작성할 수 있습니다.

 
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

:::image type="content" source="images/tutorial/azure-monitor-let-results.png" lightbox="images/tutorial/azure-monitor-let-results.png" alt-text="let 연산자 예제의 결과를 보여주는 스크린샷":::

## <a name="next-steps"></a>다음 단계

- [Kusto 쿼리 언어](samples.md?pivots=azuremonitor)에 대한 코드 샘플을 살펴봅니다.


::: zone-end
