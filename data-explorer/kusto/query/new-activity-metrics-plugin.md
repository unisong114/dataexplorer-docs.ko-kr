---
title: new_activity_metrics 플러그인 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 new_activity_metrics 플러그인에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/30/2020
ms.openlocfilehash: 0aad1c91fec6855030544596a08818b80bbf3d18
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81512209"
---
# <a name="new_activity_metrics-plugin"></a>new_activity_metrics 플러그 인

`New Users`의 코호트에 대한 유용한 활동 메트릭(고유 개 수 값, 새 값의 고유 개수, 보존률 및 이탈률)을 계산합니다. (시간 창에서 `New Users` 1위를 본 모든 사용자)의 각 코호트는 모든 이전 코호트와 비교됩니다. 비교는 이전의 *모든* 시간 창을 고려합니다. 예를 들어 from@T2 및 to=T3에 대한 레코드에서 T1과 T2 모두에서 볼 수 없었던 T3의 고유한 사용자 수가 됩니다. 
```kusto
T | evaluate new_activity_metrics(id, datetime_column, startofday(ago(30d)), startofday(now()), 1d, dim1, dim2, dim3)
```

**구문**

*T* `| evaluate` `,` `,` *End* `,` *Window* `,` `,` `,` `,` *IdColumn* `,` *Start* *TimelineColumn* *dim1* *dim2* *Cohort*IdColumn 타임라인열 시작 창 [ 코호트] [ dim1 dim2 ...] `new_activity_metrics(` [`,` *뒤로*]`)`

**인수**

* *T*: 입력 테이블 형식 식입니다.
* IdColumn : 사용자 활동을 나타내는 ID 값이 있는 열의 *이름입니다.* 
* *타임라인열*: 타임라인을 나타내는 열의 이름입니다.
* *시작*: 분석 시작 기간의 값을 가진 스칼라.
* *끝*: 분석 종료 기간의 값을 가진 스칼라.
* *창*: 분석 창 기간의 값을 가진 스칼라. 숫자 /날짜 시간 / 타임 스탬프 값 또는 중 하나인 `week` / `month` / `year`문자열일 수 있으며, 이 경우 모든 마침표는 그에 따라 [startofweek](startofweekfunction.md)/[startofmonth](startofmonthfunction.md)/[startofyear가](startofyearfunction.md) 됩니다. 
* *코호트*: (선택 사항) 특정 코호트를 나타내는 스칼라 상수입니다. 제공되지 않으면 분석 시간 창에 해당하는 모든 코호트가 계산되고 반환됩니다.
* *dim1*, *dim2*, ...: 활동 메트릭 계산을 슬라이스하는 차원 열의 (선택 사항) 목록입니다.
* *뒤로*: (선택 사항) '뒤로 보기' 기간에 속하는 일련의 아이디가 있는 표식식

**반환**

'from' 및 'to' 타임라인 기간 및 각 기존 차원 조합의 각 조합에 대해 고유한 개수 값, 고유한 새 값 수, 보존율 및 이탈률이 있는 테이블을 반환합니다.

출력 테이블 스키마는 다음과 같은 것입니다.

|from_TimelineColumn|to_TimelineColumn|dcount_new_values|dcount_retained_values|dcount_churn_values|retention_rate|churn_rate|어둡게 1|..|dim_n|
|---|---|---|---|---|---|---|---|---|---|
|유형: *타임라인열* 현재|동일|long|long|double|double|double|..|..|..|

* `from_TimelineColumn`- 새로운 사용자의 코호트. 이 레코드의 메트릭은 이 기간에 처음 본 모든 사용자를 참조합니다. *처음 볼* 수 있는 결정은 분석 기간의 모든 이전 기간을 고려합니다. 
* `to_TimelineColumn`- 비교되는 기간입니다. 
* `dcount_new_values`- 이전 및 포함 `to_TimelineColumn` `from_TimelineColumn`하기 전에 *모든* 기간에 볼 수 없는 별개의 사용자의 수입니다. 
* `dcount_retained_values`- 모든 신규 사용자 중에서 `from_TimelineColumn`처음 본 , 에서 본 `to_TimelineCoumn`별개의 사용자 수 .
* `dcount_churn_values`- 모든 신규 사용자 중에서 `from_TimelineColumn`처음 본 , 에서 볼 `to_TimelineCoumn`수 *없었던* 별개의 사용자 수 .
* `retention_rate`- 코호트 `dcount_retained_values` 에서 퍼센트 (사용자가 처음 `from_TimelineColumn`본).
* `churn_rate`- 코호트 `dcount_churn_values` 에서 퍼센트 (사용자가 처음 `from_TimelineColumn`본).

**참고 사항**

정의 `Churn Rate` 및 `Retention Rate` - activity_metrics [플러그인](./activity-metrics-plugin.md) 설명서의 **메모** 섹션을 참조하십시오.


**예**

다음 샘플 데이터 집합은 어떤 사용자가 어떤 날에 보았는가를 보여 주며, 이 에 대해 보여 주시면 됩니다. 테이블은 다음과 같이 원본 `Users` 테이블을 기반으로 생성되었습니다. 

```kusto
Users | summarize tostring(make_set(user)) by bin(Timestamp, 1d) | order by Timestamp asc;
```

|타임스탬프|set_user|
|---|---|
|2019-11-01 00:00:00.0000000|[0,2,3,4]|
|2019-11-02 00:00:00.0000000|[0,1,3,4,5]|
|2019-11-03 00:00:00.0000000|[0,2,4,5]|
|2019-11-04 00:00:00.0000000|[0,1,2,3]|
|2019-11-05 00:00:00.0000000|[0,1,2,3,4]|

원래 테이블에 대 한 플러그인의 출력은 다음과 같은: 

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

다음은 출력에서 몇 가지 레코드를 분석한 것입니다. 
* 레코드 `R=3` `from_TimelineColumn`  =  `2019-11-01`, `to_TimelineColumn`  =  `2019-11-03`, :
    * 이 레코드에 대해 고려된 사용자는 모두 11/1에 있는 새로운 사용자입니다. 이 기간은 첫 번째 기간이므로 이 모든 사용자가 해당 저장소에 있습니다 – [0,2,3,4]
    * `dcount_new_values`– 에 사용자의 수 11/3 누가에 보이지 않았다 11/1. 여기에는 단일 사용자 `5`- . 
    * `dcount_retained_values`– 에 모든 새로운 사용자 중 11/1, 얼마나 많은 까지 유지 되었다 11/3? 세 가지가`[0,2,4]`있습니다 `count_churn_values` (), 하나`3`(사용자 =)입니다. 
    * `retention_rate`= 0.75 – 11/1에서 처음 본 4명의 신규 사용자 중 3명이 사용자를 유지했습니다. 

* 레코드 `R=9` `from_TimelineColumn`  =  `2019-11-02`, `to_TimelineColumn`  =  `2019-11-04`, :
    * 이 레코드는 11/2에서 처음 보였던 새로운 `1` 사용자에 `5`초점을 맞춥니다. 
    * `dcount_new_values`– 에 사용자의 수 11/4 누가 모든 기간을 `T0 .. from_Timestamp`통해 볼 수 없습니다 . 의미, 11/4에 볼 수 있지만 11/1 또는 11/2에서 볼 수 없는 사용자 - 그러한 사용자가 없습니다. 
    * `dcount_retained_values`– 에 모든 새로운 사용자 중`[1,5]`11/2 (), 얼마나 많은 까지 유지 되었다 11/4? 이러한 사용자 ()`[1]`중 하나가 count_churn_values 하나 `5`(사용자)입니다. 
    * `retention_rate`0.5 – 11/2에 두 개의 새로운 것 들 중 11/4에 유지 된 단일 사용자. 


### <a name="weekly-retention-rate-and-churn-rate-single-week"></a>주간 유지율 및 이탈률(1주일)

다음 쿼리는 코호트(첫 주에 도착한 사용자)에 `New Users` 대한 주별 기간의 보존 및 이탈률을 계산합니다.

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


### <a name="weekly-retention-rate-and-churn-rate-complete-matrix"></a>주간 유지율 및 이탈률(전체 매트릭스)

다음 쿼리는 코호트에 대한 주별 기간의 `New Users` 보존 및 이탈률을 계산합니다. 이전 예제에서 1주일 동안 통계를 계산한 경우 아래는 각 에서/에서 조합에 대한 NxN 테이블을 생성합니다.

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


### <a name="weekly-retention-rate-with-lookback-period"></a>회조회 기간이 있는 주간 유지율

다음 쿼리는 코호트를 `New Users` 정의하는 데 사용되는 ID `lookback` 집합이 있는 테이블 형식 쿼리(이 집합에 `New Users` 나타나지 않는 모든 ID는)를 `New Users`고려할 때 코호트의 보존률을 계산합니다. 쿼리는 분석 기간 `New Users` 동안의 보존 동작을 검사합니다.

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
