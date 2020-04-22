---
title: 자습서 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 자습서에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/23/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 4720d44396fbb30350a4113fa798d7d179d7ae85
ms.sourcegitcommit: 01eb9aaf1df2ebd5002eb7ea7367a9ef85dc4f5d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81765773"
---
# <a name="tutorial"></a>자습서

::: zone pivot="azuredataexplorer"

Kusto 쿼리 언어에 대해 알아보는 가장 좋은 방법은 [일부 샘플 데이터가 있는 데이터베이스를](https://help.kusto.windows.net/Samples)사용하여 언어에 대한 "느낌"을 얻기 위해 몇 가지 간단한 쿼리를 보는 것입니다. 이 문서에서 보여 주는 쿼리는 해당 데이터베이스에서 실행되어야 합니다. 이 `StormEvents` 샘플 데이터베이스의 표는 미국에서 발생한 폭풍에 대한 몇 가지 정보를 제공합니다.

<!--
  TODO: Provide link to reference data we used originally in StormEvents
-->

<!--
  TODO: A few samples below reference non-existent tables, such as Events and Logs.
        We need to add these tables.
-->

## <a name="count-rows"></a>행 수 계산

예제 데이터베이스에는 .라는 `StormEvents`테이블이 있습니다.
얼마나 큰지 알아내기 위해 해당 콘텐츠를 단순히 행을 계산하는 연산자로 파이프합니다.

* *구문:* 쿼리는 데이터 원본(일반적으로 테이블 이름)이며, 선택적으로 파이프 문자와 일부 테이블 형식 연산자의 하나 이상의 쌍이 뒤따릅니다.

```kusto
StormEvents | count
```

결과:

|개수|
|-----|
|59066|
    
[개수 연산자](./countoperator.md).

## <a name="project-select-a-subset-of-columns"></a>프로젝트: 열의 하위 집합선택

[프로젝트를](./projectoperator.md) 사용하여 원하는 열만 선택합니다. [프로젝트와](./projectoperator.md) [테이크](./takeoperator.md) 연산자 모두를 사용하는 아래 예제를 참조하십시오.

## <a name="where-filtering-by-a-boolean-expression"></a>위치: 부울 식으로 필터링

의 2 월 `flood`-2007 동안에 `California` 만 s를 보자 :

```kusto
StormEvents
| where StartTime > datetime(2007-02-01) and StartTime < datetime(2007-03-01)
| where EventType == 'Flood' and State == 'CALIFORNIA'
| project StartTime, EndTime , State , EventType , EpisodeNarrative
```

|StartTime|EndTime|시스템 상태|EventType|에피소드 내러티브|
|---|---|---|---|---|
|2007-02-19 00:00:00.0000000|2007-02-19 08:00:00.0000000|캘리포니아|홍수|남부 산 호아킨 밸리를 가로 질러 이동 정면 시스템은 19 일 의 이른 아침 시간에 서부 컨 카운티에 폭우의 짧은 기간을 가져왔다. 작은 홍수는 태프트 근처 주 고속도로 166에 걸쳐보고되었다.|

## <a name="take-show-me-n-rows"></a>테이크 : 나에게 N 행을 보여

샘플 5 행에 무엇이 있는지 - 일부 데이터를 보자?

```kusto
StormEvents
| take 5
| project  StartTime, EndTime, EventType, State, EventNarrative  
```

|StartTime|EndTime|EventType|시스템 상태|EventNarrative|
|---|---|---|---|---|
|2007-09-18 20:00:00.0000000|2007-09-19 18:00:00.0000000|호우|플로리다|볼루시아 카운티 해안 의 일부에 걸쳐 24 시간 기간에 비의 9 인치만큼 떨어졌다.|
|2007-09-20 21:57:00.0000000|2007-09-20 22:05:00.0000000|토네이도|플로리다|웨스트 크룩트 호수의 북쪽 끝에 있는 유스티스 마을에 토네이도가 떨어졌다. 토네이도는 유스티스(Eustis)를 통해 북서쪽으로 이동하면서 EF1 강도로 빠르게 강해졌다. 트랙은 길이가 2마일 미만이었고 최대 너비는 300야드였습니다.  토네이도는 7 가정을 파괴했다. 27가구중 큰 피해를 입었으며 81가구가 경미한 피해를 입었다고 보고했다. 심각한 부상은 없었으며 재산 피해는 620만 달러로 책정되었습니다.|
|2007-09-29 08:11:00.0000000|2007-09-29 08:11:00.0000000|워터스푸드|애틀랜틱 사우스|멜버른 비치의 대서양 남동쪽에 형성된 물주둥이가 잠시 해안쪽으로 움직였다.|
|2007-12-20 07:50:00.0000000|2007-12-20 07:53:00.0000000|뇌우를 동반한 바람|미시시피|수많은 큰 나무들이 전력선에 쓰러져 쓰러졌다. 피해는 동부 애덤스 카운티에서 발생했습니다.|
|2007-12-30 16:00:00.0000000|2007-12-30 16:05:00.0000000|뇌우를 동반한 바람|그루지야|카운티 디스패치는 주 도로 206 근처의 퀸시 배튼 루프를 따라 여러 그루의 나무가 날아갔다고 보고했다. 나무 제거 비용은 추정되었다.|

그러나 [take](./takeoperator.md) 특별한 순서로 테이블에서 행을 표시하므로 정렬해 보겠습니다.
* [제한은](./takeoperator.md) [테이크의](./takeoperator.md) 별칭이며 동일한 효과를 갖습니다.

## <a name="sort-and-top"></a>정렬 및 상단

* *구문:* 일부 연산자는 와 같은 `by`키워드로 매개 변수를 도입했습니다.
* `desc`은(는) 내림차순을 의미하고 `asc`은(는) 오름차순을 의미합니다.

특정 열을 기준으로 순서가 정해진 처음 n 행 표시:

```kusto
StormEvents
| top 5 by StartTime desc
| project  StartTime, EndTime, EventType, State, EventNarrative  
```

|StartTime|EndTime|EventType|시스템 상태|EventNarrative|
|---|---|---|---|---|
|2007-12-31 22:30:00.0000000|2007-12-31 23:59:00.0000000|겨울 폭풍|미시간|이 폭설 이벤트는 새해 첫날 이른 아침 까지 계속되었습니다.|
|2007-12-31 22:30:00.0000000|2007-12-31 23:59:00.0000000|겨울 폭풍|미시간|이 폭설 이벤트는 새해 첫날 이른 아침 까지 계속되었습니다.|
|2007-12-31 22:30:00.0000000|2007-12-31 23:59:00.0000000|겨울 폭풍|미시간|이 폭설 이벤트는 새해 첫날 이른 아침 까지 계속되었습니다.|
|2007-12-31 23:53:00.0000000|2007-12-31 23:53:00.0000000|강풍|캘리포니아|벤투라 카운티의 산에서 북동풍이 약 58mph로 불어났습니다.|
|2007-12-31 23:53:00.0000000|2007-12-31 23:53:00.0000000|강풍|캘리포니아|웜 스프링스 RAWS 센서는 북서풍이 58mph로 돌풍을 동반했다고 보고했습니다.|

[정렬을](./sortoperator.md) 사용하여 동일한 것을 달성 한 [다음](./takeoperator.md) 연산자

```kusto
StormEvents
| sort by StartTime desc
| take 5
| project  StartTime, EndLat, EventType, EventNarrative
```

## <a name="extend-compute-derived-columns"></a>확장: 파생 열 계산

모든 행에서 값을 계산하여 새 열을 만듭니다.

```kusto
StormEvents
| limit 5
| extend Duration = EndTime - StartTime 
| project StartTime, EndTime, Duration, EventType, State
```

|StartTime|EndTime|Duration|EventType|시스템 상태|
|---|---|---|---|---|
|2007-09-18 20:00:00.0000000|2007-09-19 18:00:00.0000000|22:00:00|호우|플로리다|
|2007-09-20 21:57:00.0000000|2007-09-20 22:05:00.0000000|00:08:00|토네이도|플로리다|
|2007-09-29 08:11:00.0000000|2007-09-29 08:11:00.0000000|00:00:00|워터스푸드|애틀랜틱 사우스|
|2007-12-20 07:50:00.0000000|2007-12-20 07:53:00.0000000|00:03:00|뇌우를 동반한 바람|미시시피|
|2007-12-30 16:00:00.0000000|2007-12-30 16:05:00.0000000|00:05:00|뇌우를 동반한 바람|그루지야|

열 이름을 다시 사용하고 동일한 열에 계산 결과를 할당할 수 있습니다.
다음은 그 예입니다.

```kusto
print x=1
| extend x = x + 1, y = x
| extend x = x + 1
```

|x|y|
|---|---|
|3|1|

[스칼라 식에는](./scalar-data-types/index.md) 모든 일반적인 연산자`+`(, `*` `/`, `%` `-`및 기타) 함수가 포함될 수 있으며 다양한 유용한 함수가 있습니다.

## <a name="summarize-aggregate-groups-of-rows"></a>요약: 행 의 집계 그룹

각 국가에서 발생한 이벤트 수를 계산합니다.

```kusto
StormEvents
| summarize event_count = count() by State
```

절에서 동일한 값을 가진 행을 함께 [요약한](./summarizeoperator.md) 다음 집계 함수(예:)를 `count`사용하여 각 그룹을 단일 행으로 결합합니다. `by` 따라서 이 경우 각 상태에 대한 행과 해당 상태의 행 수에 대한 열이 있습니다.

집계 함수의 범위가 있으며, 하나의 요약 연산자에서 여러 개의 집계 [함수를](./summarizeoperator.md#list-of-aggregation-functions)사용하여 여러 개의 계산된 열을 생성할 수 있습니다. 예를 들어, 각 주에서 폭풍의 수와 주당 고유한 유형의 폭풍의 합계를 얻을 수 있습니다.  
그런 다음 [top을](./topoperator.md) 사용하여 가장 폭풍에 영향을 받는 상태를 얻을 수 있습니다.

```kusto
StormEvents 
| summarize StormCount = count(), TypeOfStorms = dcount(EventType) by State
| top 5 by StormCount desc
```

|시스템 상태|스톰카운트|폭풍의 유형|
|---|---|---|
|텍사스|4701|27|
|캔자스|3166|21|
|아이오와|2337|19|
|일리노이|2022|23|
|미주리|2016|20|

summarize의 결과에 포함된 내용:

* `by`에 명명된 각 열
* 계산된 각 식에 대한 열입니다.
* 각 `by` 값 조합에 대한 행

## <a name="summarize-by-scalar-values"></a>스칼라 값에 의해 요약

`by` 절에서 스칼라(숫자, 시간 또는 간격) 값을 사용할 수 있지만 값을 저장소에 넣을 수 있습니다.  
[bin()](./binfunction.md) 함수는 다음과 같은 데 유용합니다.

```kusto
StormEvents
| where StartTime > datetime(2007-02-14) and StartTime < datetime(2007-02-21)
| summarize event_count = count() by bin(StartTime, 1d)
```

이렇게 하면 모든 타임스탬프가 1일 간격으로 줄어듭니다.

|StartTime|event_count|
|---|---|
|2007-02-14 00:00:00.0000000|180|
|2007-02-15 00:00:00.0000000|66|
|2007-02-16 00:00:00.0000000|164|
|2007-02-17 00:00:00.0000000|103|
|2007-02-18 00:00:00.0000000|22|
|2007-02-19 00:00:00.0000000|52|
|2007-02-20 00:00:00.0000000|60|

[빈()은](./binfunction.md) 여러 언어의 [floor()](./floorfunction.md) 기능과 동일합니다. [요약을](./summarizeoperator.md) 통해 그룹에 행을 할당할 수 있도록 모든 값을 제공하는 계수의 가장 가까운 배수로 줄어듭니다.

## <a name="render-display-a-chart-or-table"></a>렌더링: 차트 또는 테이블 표시

두 개의 열을 투영하고 차트의 x 및 y 축으로 사용합니다.

```kusto
StormEvents 
| summarize event_count=count(), mid = avg(BeginLat) by State 
| sort by mid
| where event_count > 1800
| project State, event_count
| render columnchart
```

:::image type="content" source="images/tour/060.png" alt-text="060":::

프로젝트 작업에서 `mid` 제거되었지만 차트에 해당 순서로 국가를 표시하려면 여전히 필요합니다.

엄밀히 말하면 '렌더'는 쿼리 언어의 일부가 아니라 클라이언트의 기능입니다. 여전히, 그것은 언어에 통합 하 고 결과 구상에 대 한 매우 유용.


## <a name="timecharts"></a>시간 차트

숫자 저장소로 돌아가서 타임시리즈를 표시해 보겠습니다.

```kusto
StormEvents
| summarize event_count=count() by bin(StartTime, 1d)
| render timechart
```

:::image type="content" source="images/tour/080.png" alt-text="080":::

## <a name="multiple-series"></a>여러 계열

`summarize by` 절에 여러 값을 사용하여 각 값 조합에 대해 별도의 행을 생성합니다.

```kusto
StormEvents 
| where StartTime > datetime(2007-06-04) and StartTime < datetime(2007-06-10) 
| where Source in ("Source","Public","Emergency Manager","Trained Spotter","Law Enforcement")
| summarize count() by bin(StartTime, 10h), Source
```

:::image type="content" source="images/tour/090.png" alt-text="090":::

위의 렌더링 용어를 추가하기만 `| render timechart`하면 됩니다.

:::image type="content" source="images/tour/100.png" alt-text="100":::

첫 `render timechart` 번째 열을 x축으로 사용한 다음 다른 열을 별도의 선으로 표시합니다.

## <a name="daily-average-cycle"></a>일일 평균 주기

활동은 평균 하루에 어떻게 달라지나요?

시간 으로 비닝 시간 하루 에 의해 이벤트를 계산합니다. 휴지통 `floor` 대신 사용합니다.

```kusto
StormEvents
| extend hour = floor(StartTime % 1d , 1h)
| summarize event_count=count() by hour
| sort by hour asc
| render timechart
```

:::image type="content" source="images/tour/120.png" alt-text="120":::

현재 `render` 기간에 레이블이 제대로 지정되지 는 `| render columnchart` 않지만 대신 사용할 수 있습니다.

:::image type="content" source="images/tour/110.png" alt-text="110":::

## <a name="compare-multiple-daily-series"></a>여러 개의 일일 계열 비교

활동은 다른 주에서 하루 중 시간에 어떻게 변화합니까?

```kusto
StormEvents
| extend hour= floor( StartTime % 1d , 1h)
| where State in ("GULF OF MEXICO","MAINE","VIRGINIA","WISCONSIN","NORTH DAKOTA","NEW JERSEY","OREGON")
| summarize event_count=count() by hour, State
| render timechart
```

:::image type="content" source="images/tour/130.png" alt-text="130":::

`1h` x축을 지속 시간 대신 시간 번호로 변환하려면 다음을 나눕니다.

```kusto
StormEvents
| extend hour= floor( StartTime % 1d , 1h)/ 1h
| where State in ("GULF OF MEXICO","MAINE","VIRGINIA","WISCONSIN","NORTH DAKOTA","NEW JERSEY","OREGON")
| summarize event_count=count() by hour, State
| render columnchart
```

:::image type="content" source="images/tour/140.png" alt-text="140":::

## <a name="join"></a>join

두 가지 상태 모두에서 두 개의 주어진 EventType을 찾는 방법은 무엇입니까?

첫 번째 EventType 및 두 번째 EventType을 사용하면 폭풍 이벤트를 끌어온 다음 상태에서 두 집합에 참여할 수 있습니다.

```kusto
StormEvents
| where EventType == "Lightning"
| join (
    StormEvents 
    | where EventType == "Avalanche"
) on State  
| distinct State
```

:::image type="content" source="images/tour/145.png" alt-text="145":::

## <a name="user-session-example-of-join"></a>조인의 사용자 세션 예

이 섹션에서는 `StormEvents` 테이블을 사용하지 않습니다.

각 사용자 세션의 시작 및 끝을 표시하는 이벤트가 포함된 데이터와 각 세션에 대한 고유 ID가 있다고 가정합니다. 

각 사용자 세션은 얼마나 오래 지속됩니까?

두 `extend` 타임스탬프에 대한 별칭을 제공하는 데 사용하여 세션 기간을 계산할 수 있습니다.

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

:::image type="content" source="images/tour/150.png" alt-text="150":::

조인을 수행하기 전에 `project`을(를) 사용하여 필요한 열만 선택하는 것이 좋습니다.
동일한 절에서 타임스탬프 열의 이름을 바꿉니다.

## <a name="plot-a-distribution"></a>분포 출력

길이가 얼마나 다른가요?

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

:::image type="content" source="images/tour/170.png" alt-text="170":::

또는 `| render columnchart`사용 :

:::image type="content" source="images/tour/160.png" alt-text="160":::

## <a name="percentiles"></a>백분위수

폭풍의 다른 비율을 커버 하는 기간의 어떤 범위?

위의 쿼리를 사용하지만 `render` 다음으로 바꿉꿉을 사용합니다.

```kusto
| summarize percentiles(duration, 5, 20, 50, 80, 95)
```

이 경우 절을 `by` 제공하지 않으므로 결과는 단일 행입니다.

:::image type="content" source="images/tour/180.png" alt-text="180":::

확인할 수 있는 사항:

* 폭풍의 5%는 5m 미만의 지속 시간을 가며,
* 폭풍의 50 %는 1h 25m 미만 지속;
* 폭풍의 5 %는 적어도 2시간 50m 지속됩니다.

각 상태에 대해 별도의 분석 데이터를 얻으려면 두 요약 연산자모두를 통해 상태 열을 별도로 가져와야합니다.

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

:::image type="content" source="images/tour/190.png" alt-text="190":::

## <a name="let-assign-a-result-to-a-variable"></a>Let: 변수에 결과 할당

[let을](./letstatement.md) 사용하여 위의 'join' 예제에서 쿼리 식의 일부를 구분합니다. 결과는 변하지 않음:

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

> 팁: Kusto 클라이언트에서는 이 부분 사이에 빈 줄을 두지 마십시오. 이 부분을 모두 실행해야 합니다.

## <a name="combining-data-from-several-databases-in-a-query"></a>쿼리에서 여러 데이터베이스의 데이터 결합

자세한 [토론은 데이터베이스 간 쿼리](./cross-cluster-or-database-queries.md) 를 참조하십시오.

스타일의 쿼리를 작성할 때:

```kusto
Logs | where ...
```

Logs라는 테이블은 기본 데이터베이스에 있어야 합니다. 다른 데이터베이스의 테이블에 액세스하려면 다음 구문을 사용합니다.

```kusto
database("db").Table
```

따라서 *진단* 및 *원격 분석이라는* 데이터베이스가 있고 일부 데이터를 상호 연관시키려면 작성할 수 있습니다(진단이 기본 데이터베이스라고 가정). *Diagnostics*

```kusto
Logs | join database("Telemetry").Metrics on Request MachineId | ...
```

또는 기본 데이터베이스가 *원격 분석인* 경우

```kusto
union Requests, database("Diagnostics").Logs | ...
```
    
위의 모든 데이터베이스는 두 데이터베이스가 현재 연결된 클러스터에 있다고 가정했습니다. 원격 *분석* 데이터베이스가 *TelemetryCluster.kusto.windows.net* 라는 다른 클러스터에 속해 있다고 가정하면 액세스해야 합니다.

```kusto
Logs | join cluster("TelemetryCluster").database("Telemetry").Metrics on Request MachineId | ...
```

> 참고: 클러스터를 지정하면 데이터베이스가 필수입니다.

::: zone-end

::: zone pivot="azuremonitor"

Azure 모니터에서는 이 성능이 지원되지 않습니다.

::: zone-end
