---
title: new_activity_metrics 플러그 인-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 new_activity_metrics 플러그 인을 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/30/2020
ms.openlocfilehash: b376afda0874fdb70934ffc6861192ef9028e9aa
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347088"
---
# <a name="new_activity_metrics-plugin"></a>new_activity_metrics 플러그 인

의 코 호트에 대 한 유용한 활동 메트릭 (고유 카운트 값, 새 값의 고유 개수, 보존 률 및 변동 율)을 계산 `New Users` 합니다. 의 각 코 호트 `New Users` (시간 창에 표시 되는 모든 사용자)는 이전 코 호트 모두와 비교 됩니다. 비교는 이전 시간 창을 *모두* 고려 합니다. 예를 들어 from = T2 및 to = T3의 레코드에서 고유 사용자 수는 1과 T2 모두에 표시 되지 않은 T3의 모든 사용자입니다. 
```kusto
T | evaluate new_activity_metrics(id, datetime_column, startofday(ago(30d)), startofday(now()), 1d, dim1, dim2, dim3)
```

## <a name="syntax"></a>Syntax

*T* `| evaluate` `new_activity_metrics(` *idcolumn* `,` *TimelineColumn* `,` *Start* `,` *End* `,` *Window* [ `,` *코 호트*] [ `,` *dim1* `,` *dim2* ] `,` [ `,` *Lookback*]`)`

## <a name="arguments"></a>인수

* *T*: 입력 테이블 형식 식입니다.
* *Idcolumn*: 사용자 활동을 나타내는 ID 값이 포함 된 열의 이름입니다. 
* *TimelineColumn*: 타임 라인을 나타내는 열의 이름입니다.
* *Start*: 분석 시작 기간의 값을 포함 하는 스칼라입니다.
* *종료*: 분석 종료 기간의 값을 포함 하는 스칼라입니다.
* *Window*: 분석 기간 값이 포함 된 스칼라입니다. 는 숫자/날짜/시간/타임 스탬프 값 또는 중 하나인 문자열일 수 있으며,이 `week` / `month` / `year` 경우 모든 기간이 적절 한 [startofweek](startofweekfunction.md) / [startofmonth](startofmonthfunction.md) / [startofyear](startofyearfunction.md) 됩니다. 
* *코 호트*: (선택 사항) 특정 코 호트를 나타내는 스칼라 상수입니다. 지정 하지 않으면 분석 시간 창에 해당 하는 모든 코 호트 계산 되 고 반환 됩니다.
* *dim1*, *dim2*, ...: (선택 사항) 활동 메트릭 계산을 분할 하는 차원 열의 목록입니다.
* *Lookback*: (선택 사항) ' 뒤로 ' 기간에 속하는 id 집합이 있는 테이블 형식 식입니다.

## <a name="returns"></a>반환

' From ' 및 ' to ' 및 ' to ' timeline 기간의 각 조합에 대 한 고유 카운트 값, 고유 카운트 수, 보존 률 및 변동 율을 포함 하는 테이블을 반환 하 고 각 기존 차원 조합에 대해

출력 테이블 스키마는 다음과 같습니다.

|from_TimelineColumn|to_TimelineColumn|dcount_new_values|dcount_retained_values|dcount_churn_values|retention_rate|churn_rate|dim1|..|dim_n|
|---|---|---|---|---|---|---|---|---|---|
|유형: *TimelineColumn*|동일|long|long|double|double|double|..|..|..|

* `from_TimelineColumn`-새 사용자의 코 호트. 이 레코드의 메트릭은이 기간에 처음으로 표시 된 모든 사용자를 나타냅니다. *처음 표시* 되는 결정은 분석 기간의 모든 이전 기간을 고려 합니다. 
* `to_TimelineColumn`-비교 되는 기간입니다. 
* `dcount_new_values`- `to_TimelineColumn` 및를 포함 하 여 이전에 *모든* 기간에 표시 되지 않은 고유 사용자 수입니다 `from_TimelineColumn` . 
* `dcount_retained_values`에 처음으로 표시 되는 모든 새 사용자 중 `from_TimelineColumn` 에서 표시 된 고유 사용자 수입니다 `to_TimelineCoumn` .
* `dcount_churn_values`에 처음으로 표시 되는 모든 새 사용자의 경우 `from_TimelineColumn` 에는 표시 *되지* 않은 고유한 사용자 수가 표시 `to_TimelineCoumn` 됩니다.
* `retention_rate`- `dcount_retained_values` 코 호트 (에서 사용자가 처음으로 확인)의 백분율입니다 `from_TimelineColumn` .
* `churn_rate`- `dcount_churn_values` 코 호트 (에서 사용자가 처음으로 확인)의 백분율입니다 `from_TimelineColumn` .

**참고**

및에 대 한 정의는 `Retention Rate` `Churn Rate` [activity_metrics 플러그 인](./activity-metrics-plugin.md) 설명서의 **참고** 섹션을 참조 하세요.


## <a name="examples"></a>예제

다음 샘플 데이터 집합은 어떤 사용자가 어떤 날짜에 표시 되는지 보여 줍니다. 테이블은 다음과 같이 원본 테이블을 기반으로 생성 되었습니다 `Users` . 

```kusto
Users | summarize tostring(make_set(user)) by bin(Timestamp, 1d) | order by Timestamp asc;
```

|Timestamp|set_user|
|---|---|
|2019-11-01 00:00:00.0000000|[0, 2, 3, 4]|
|2019-11-02 00:00:00.0000000|[0, 1, 3, 4, 5]|
|2019-11-03 00:00:00.0000000|[0, 2, 4, 5]|
|2019-11-04 00:00:00.0000000|[0, 1, 2, 3]|
|2019-11-05 00:00:00.0000000|[0, 1, 2, 3, 4]|

원본 테이블의 플러그 인 출력은 다음과 같습니다. 

```kusto
let StartDate = datetime(2019-11-01 00:00:00);
let EndDate = datetime(2019-11-07 00:00:00);
Users 
| evaluate new_activity_metrics(user, Timestamp, StartDate, EndDate-1tick, 1d) 
| where from_Timestamp < datetime(2019-11-03 00:00:00.0000000)
```

|R|from_Timestamp|to_Timestamp|dcount_new_values|dcount_retained_values|dcount_churn_values|retention_rate|churn_rate|
|---|---|---|---|---|---|---|---|
|1|2019-11-01 00:00:00.0000000|2019-11-01 00:00:00.0000000|4|4|0|1|0|
|2|2019-11-01 00:00:00.0000000|2019-11-02 00:00:00.0000000|2|3|1|0.75|0.25|
|3|2019-11-01 00:00:00.0000000|2019-11-03 00:00:00.0000000|1|3|1|0.75|0.25|
|4|2019-11-01 00:00:00.0000000|2019-11-04 00:00:00.0000000|1|3|1|0.75|0.25|
|5|2019-11-01 00:00:00.0000000|2019-11-05 00:00:00.0000000|1|4|0|1|0|
|6|2019-11-01 00:00:00.0000000|2019-11-06 00:00:00.0000000|0|0|4|0|1|
|7|2019-11-02 00:00:00.0000000|2019-11-02 00:00:00.0000000|2|2|0|1|0|
|8|2019-11-02 00:00:00.0000000|2019-11-03 00:00:00.0000000|0|1|1|0.5|0.5|
|9|2019-11-02 00:00:00.0000000|2019-11-04 00:00:00.0000000|0|1|1|0.5|0.5|
|10|2019-11-02 00:00:00.0000000|2019-11-05 00:00:00.0000000|0|1|1|0.5|0.5|
|11|2019-11-02 00:00:00.0000000|2019-11-06 00:00:00.0000000|0|0|2|0|1|

다음은 출력에서 몇 가지 레코드를 분석 한 것입니다. 
* 레코드 `R=3` , `from_TimelineColumn`  =  `2019-11-01` , `to_TimelineColumn`  =  `2019-11-03` :
    * 이 레코드를 고려 하는 사용자는 모두 11/1에 표시 되는 모든 새 사용자입니다. 이것은 첫 번째 기간 이므로 해당 bin의 모든 사용자-[0, 2, 3, 4]입니다.
    * `dcount_new_values`– 11/1에 표시 되지 않은 11/3 사용자의 수입니다. 여기에는 단일 사용자 ()가 포함 됩니다 `5` . 
    * `dcount_retained_values`-11/1에 대 한 모든 새 사용자 중에서 11/3까지 보존 된 수는 얼마 인가요? 3 ( `[0,2,4]` )이 있고,는 `count_churn_values` 1 (사용자 = `3` )입니다. 
    * `retention_rate`= 0.75 – 처음에는 11/1에 표시 된 사용자 4 명에 게 보존 된 세 사용자가 있습니다. 

* 레코드 `R=9` , `from_TimelineColumn`  =  `2019-11-02` , `to_TimelineColumn`  =  `2019-11-04` :
    * 이 레코드는 11/2 – 사용자 및에 처음으로 표시 된 새 사용자를 중심으로 설명 `1` `5` 합니다. 
    * `dcount_new_values`– 모든 기간 동안 표시 되지 않은 11/4의 사용자 수입니다 `T0 .. from_Timestamp` . 즉, 11/4에는 있지만 11/1 또는 11/2에는 표시 되지 않은 사용자는 해당 사용자가 없습니다. 
    * `dcount_retained_values`-11/2 ()의 모든 새 사용자 중 `[1,5]` 에서 11/4까지 유지 된 수는 얼마 인가요? 이러한 사용자 ()는 하나 `[1]` 이며 count_churn_values (사용자 `5` )입니다. 
    * `retention_rate`0.5 – 11/2의 새 두 항목 중 11/4에 보존 된 단일 사용자입니다. 


### <a name="weekly-retention-rate-and-churn-rate-single-week"></a>주간 보존 률 및 변동 률 (1 주)

다음 쿼리는 `New Users` 코 호트 (첫 번째 주에 도착 한 사용자)에 대 한 주-주 기간의 보존 및 변동 율을 계산 합니다.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
// Generate random data of user activities
let _start = datetime(2017-05-01);
let _end = datetime(2017-05-31);
range Day from _start to _end  step 1d
| extend d = tolong((Day - _start)/1d)
| extend r = rand()+1
| extend _users=range(tolong(d*50*r), tolong(d*50*r+200*r-1), 1) 
| mv-expand id=_users to typeof(long) limit 1000000
// Take only the first week cohort (last parameter)
| evaluate new_activity_metrics(['id'], Day, _start, _end, 7d, _start)
| project from_Day, to_Day, retention_rate, churn_rate
```

|from_Day|to_Day|retention_rate|churn_rate|
|---|---|---|---|
|2017-05-01 00:00:00.0000000|2017-05-01 00:00:00.0000000|1|0|
|2017-05-01 00:00:00.0000000|2017-05-08 00:00:00.0000000|0.544632768361582|0.455367231638418|
|2017-05-01 00:00:00.0000000|2017-05-15 00:00:00.0000000|0.031638418079096|0.968361581920904|
|2017-05-01 00:00:00.0000000|2017-05-22 00:00:00.0000000|0|1|
|2017-05-01 00:00:00.0000000|2017-05-29 00:00:00.0000000|0|1|


### <a name="weekly-retention-rate-and-churn-rate-complete-matrix"></a>주간 보존 률 및 변동 률 (전체 행렬)

다음 쿼리는 코 호트에 대 한 주-주 기간의 보존 및 변동 율을 계산 합니다 `New Users` . 이전 예제에서 한 주에 대 한 통계를 계산 하는 경우 아래에서 각 from/to 조합에 대해 NxN 테이블을 생성 합니다.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
// Generate random data of user activities
let _start = datetime(2017-05-01);
let _end = datetime(2017-05-31);
range Day from _start to _end  step 1d
| extend d = tolong((Day - _start)/1d)
| extend r = rand()+1
| extend _users=range(tolong(d*50*r), tolong(d*50*r+200*r-1), 1) 
| mv-expand id=_users to typeof(long) limit 1000000
// Last parameter is omitted - 
| evaluate new_activity_metrics(['id'], Day, _start, _end, 7d)
| project from_Day, to_Day, retention_rate, churn_rate
```

|from_Day|to_Day|retention_rate|churn_rate|
|---|---|---|---|
|2017-05-01 00:00:00.0000000|2017-05-01 00:00:00.0000000|1|0|
|2017-05-01 00:00:00.0000000|2017-05-08 00:00:00.0000000|0.190397350993377|0.809602649006622|
|2017-05-01 00:00:00.0000000|2017-05-15 00:00:00.0000000|0|1|
|2017-05-01 00:00:00.0000000|2017-05-22 00:00:00.0000000|0|1|
|2017-05-01 00:00:00.0000000|2017-05-29 00:00:00.0000000|0|1|
|2017-05-08 00:00:00.0000000|2017-05-08 00:00:00.0000000|1|0|
|2017-05-08 00:00:00.0000000|2017-05-15 00:00:00.0000000|0.405263157894737|0.594736842105263|
|2017-05-08 00:00:00.0000000|2017-05-22 00:00:00.0000000|0.227631578947368|0.772368421052632|
|2017-05-08 00:00:00.0000000|2017-05-29 00:00:00.0000000|0|1|
|2017-05-15 00:00:00.0000000|2017-05-15 00:00:00.0000000|1|0|
|2017-05-15 00:00:00.0000000|2017-05-22 00:00:00.0000000|0.785488958990536|0.214511041009464|
|2017-05-15 00:00:00.0000000|2017-05-29 00:00:00.0000000|0.237644584647739|0.762355415352261|
|2017-05-22 00:00:00.0000000|2017-05-22 00:00:00.0000000|1|0|
|2017-05-22 00:00:00.0000000|2017-05-29 00:00:00.0000000|0.621835443037975|0.378164556962025|
|2017-05-29 00:00:00.0000000|2017-05-29 00:00:00.0000000|1|0|


### <a name="weekly-retention-rate-with-lookback-period"></a>Lookback 기간의 주간 보존 률

다음 쿼리는 기간 고려 시 코 호트의 보존 률을 계산 합니다 `New Users` `lookback` . 코 호트를 정의 하는 데 사용 되는 id 집합이 있는 테이블 형식 쿼리 `New Users` (이 집합에는 표시 되지 않는 모든 id `New Users` )가 있습니다. 이 쿼리는 분석 기간 동안의 보존 동작을 검사 합니다 `New Users` .

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
// Generate random data of user activities
let _lookback = datetime(2017-02-01);
let _start = datetime(2017-05-01);
let _end = datetime(2017-05-31);
let _data = range Day from _lookback to _end  step 1d
| extend d = tolong((Day - _lookback)/1d)
| extend r = rand()+1
| extend _users=range(tolong(d*50*r), tolong(d*50*r+200*r-1), 1) 
| mv-expand id=_users to typeof(long) limit 1000000;
//
let lookback_data = _data | where Day < _start | project Day, id;
_data
| evaluate new_activity_metrics(id, Day, _start, _end, 7d, _start, lookback_data)
| project from_Day, to_Day, retention_rate
```

|from_Day|to_Day|retention_rate|
|---|---|---|
|2017-05-01 00:00:00.0000000|2017-05-01 00:00:00.0000000|1|
|2017-05-01 00:00:00.0000000|2017-05-08 00:00:00.0000000|0.404081632653061|
|2017-05-01 00:00:00.0000000|2017-05-15 00:00:00.0000000|0.257142857142857|
|2017-05-01 00:00:00.0000000|2017-05-22 00:00:00.0000000|0.296326530612245|
|2017-05-01 00:00:00.0000000|2017-05-29 00:00:00.0000000|0.0587755102040816|
