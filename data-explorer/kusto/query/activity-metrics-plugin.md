---
title: activity_metrics 플러그인 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 activity_metrics 플러그인에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 940320b7cd77ba09b31192853da9073511b33f5d
ms.sourcegitcommit: 29018b3db4ea7d015b1afa65d49ecf918cdff3d6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2020
ms.locfileid: "82030267"
---
# <a name="activity_metrics-plugin"></a>activity_metrics 플러그 인

현재 기간 기간과 이전 기간 기간을 기준으로 유용한 활동 메트릭(고유 개수 값, 고유 개수, 유지율 및 이탈률)을 계산합니다(모든 시간 창이 *모든* 이전 창과 비교되는 [activity_counts_metrics 플러그인과](activity-counts-metrics-plugin.md) 달리).

```kusto
T | evaluate activity_metrics(id, datetime_column, startofday(ago(30d)), startofday(now()), 1d, dim1, dim2, dim3)
```

**구문**

*T* `| evaluate` `,` `,` *End*`,``,` `,` `,` *IdColumn* `,` *Window* *Start* *TimelineColumn* *dim2* *dim1* IdColumn 타임라인열 [ 시작 종료 ] 창 [ dim1 dim2 ...] `activity_metrics(``)`

**인수**

* *T*: 입력 테이블 형식 식입니다.
* IdColumn : 사용자 활동을 나타내는 ID 값이 있는 열의 *이름입니다.* 
* *타임라인열*: 타임라인을 나타내는 열의 이름입니다.
* *시작*: (선택 사항) 분석 시작 기간의 값을 가진 스칼라.
* *끝*: (선택 사항) 분석 종료 기간의 값을 가진 스칼라.
* *창*: 분석 창 기간의 값을 가진 스칼라. 숫자 /날짜 시간 / 타임 스탬프 값 또는 중 하나인 `week` / `month` / `year`문자열일 수 있으며, 이 경우 모든 마침표는 그에 따라 [startofweek](startofweekfunction.md)/[startofmonth](startofmonthfunction.md)/[startofyear가](startofyearfunction.md) 됩니다. 
* *dim1*, *dim2*, ...: 활동 메트릭 계산을 슬라이스하는 차원 열의 (선택 사항) 목록입니다.

**반환**

각 타임라인 기간 및 각 기존 차원 조합에 대해 고유한 개수 값, 고유한 새 값 수, 보존율 및 이탈률이 있는 테이블을 반환합니다.

출력 테이블 스키마는 다음과 같은 것입니다.

|*타임라인열*|dcount_values|dcount_newvalues|retention_rate|churn_rate|어둡게 1|..|dim_n|
|---|---|---|---|---|--|--|--|--|--|--|
|유형: *타임라인열* 현재|long|long|double|double|..|..|..|

**참고 사항**

***보존 속도 정의***

`Retention Rate`기간 동안 다음과 같이 계산됩니다.

    # of customers returned during the period
    / (divided by)
    # customers at the beginning of the period

`# of customers returned during the period` 여기서 는 다음과 같이 정의됩니다.

    # of customers at end of period
    - (minus)
    # of new customers acquired during the period

`Retention Rate`0.0에서 1.0까지 다양할 수 있습니다.  
점수가 높을수록 재방문 사용자의 양이 커야 합니다.


***이탈률 정의***

`Churn Rate`기간 동안 다음과 같이 계산됩니다.
    
    # of customers lost in the period
    / (divided by)
    # of customers at the beginning of the period

`# of customer lost in the period` 여기서 는 다음과 같이 정의됩니다.

    # of customers at the beginning of the period
    - (minus)
    # of customers at the end of the period

`Churn Rate`0.0에서 1.0까지 다양할 수 있습니다.

***이탈 률 과 보존율***

의 정의에서 `Churn Rate` 파생된 `Retention Rate`및 , 다음의 내용은 항상 true입니다.

    [Retention rate] = 100.0% - [Churn Rate]


**예**

### <a name="weekly-retention-rate-and-churn-rate"></a>주간 유지율 및 이탈률

다음 쿼리는 주별 기간동안보존율과 이탈률을 계산합니다.

```kusto
// Generate random data of user activities
let _start = datetime(2017-01-02);
let _end = datetime(2017-05-31);
range _day from _start to _end  step 1d
| extend d = tolong((_day - _start)/1d)
| extend r = rand()+1
| extend _users=range(tolong(d*50*r), tolong(d*50*r+200*r-1), 1) 
| mv-expand id=_users to typeof(long) limit 1000000
// 
| evaluate activity_metrics(['id'], _day, _start, _end, 7d)
| project _day, retention_rate, churn_rate
| render timechart 
```

|_day|retention_rate|churn_rate|
|---|---|---|
|2017-01-02 00:00:00.0000000|NaN|NaN|
|2017-01-09 00:00:00.0000000|0.179910044977511|0.820089955022489|
|2017-01-16 00:00:00.0000000|0.744374437443744|0.255625562556256|
|2017-01-23 00:00:00.0000000|0.612096774193548|0.387903225806452|
|2017-01-30 00:00:00.0000000|0.681141439205955|0.318858560794045|
|2017-02-06 00:00:00.0000000|0.278145695364238|0.721854304635762|
|2017-02-13 00:00:00.0000000|0.223172628304821|0.776827371695179|
|2017-02-20 00:00:00.0000000|0.38|0.62|
|2017-02-27 00:00:00.0000000|0.295519001701645|0.704480998298355|
|2017-03-06 00:00:00.0000000|0.280387770320656|0.719612229679344|
|2017-03-13 00:00:00.0000000|0.360628154795289|0.639371845204711|
|2017-03-20 00:00:00.0000000|0.288008028098344|0.711991971901656|
|2017-03-27 00:00:00.0000000|0.306134969325153|0.693865030674847|
|2017-04-03 00:00:00.0000000|0.356866537717602|0.643133462282398|
|2017-04-10 00:00:00.0000000|0.495098039215686|0.504901960784314|
|2017-04-17 00:00:00.0000000|0.198296836982968|0.801703163017032|
|2017-04-24 00:00:00.0000000|0.0618811881188119|0.938118811881188|
|2017-05-01 00:00:00.0000000|0.204657727593507|0.795342272406493|
|2017-05-08 00:00:00.0000000|0.517391304347826|0.482608695652174|
|2017-05-15 00:00:00.0000000|0.143667296786389|0.856332703213611|
|2017-05-22 00:00:00.0000000|0.199122325836533|0.800877674163467|
|2017-05-29 00:00:00.0000000|0.063468992248062|0.936531007751938|

:::image type="content" source="images/activity-metrics-plugin/activity-metrics-churn-and-retention.png" border="false" alt-text="활동 메트릭 이탈 및 보존":::

### <a name="distinct-values-and-distinct-new-values"></a>뚜렷한 값과 뚜렷한 '새' 값 

다음 쿼리는 주별 기간에 대해 고유 값과 'new' 값(이전 시간 기간에 나타나지 않은 ID)을 계산합니다.


```kusto
// Generate random data of user activities
let _start = datetime(2017-01-02);
let _end = datetime(2017-05-31);
range _day from _start to _end  step 1d
| extend d = tolong((_day - _start)/1d)
| extend r = rand()+1
| extend _users=range(tolong(d*50*r), tolong(d*50*r+200*r-1), 1) 
| mv-expand id=_users to typeof(long) limit 1000000
// 
| evaluate activity_metrics(['id'], _day, _start, _end, 7d)
| project _day, dcount_values, dcount_newvalues
| render timechart 
```

|_day|dcount_values|dcount_newvalues|
|---|---|---|
|2017-01-02 00:00:00.0000000|630|630|
|2017-01-09 00:00:00.0000000|738|575|
|2017-01-16 00:00:00.0000000|1187|841|
|2017-01-23 00:00:00.0000000|1092|465|
|2017-01-30 00:00:00.0000000|1261|647|
|2017-02-06 00:00:00.0000000|1744|1043|
|2017-02-13 00:00:00.0000000|1563|432|
|2017-02-20 00:00:00.0000000|1406|818|
|2017-02-27 00:00:00.0000000|1956|1429|
|2017-03-06 00:00:00.0000000|1593|848|
|2017-03-13 00:00:00.0000000|1801|1423|
|2017-03-20 00:00:00.0000000|1710|1017|
|2017-03-27 00:00:00.0000000|1796|1516|
|2017-04-03 00:00:00.0000000|1381|1008|
|2017-04-10 00:00:00.0000000|1756|1162|
|2017-04-17 00:00:00.0000000|1831|1409|
|2017-04-24 00:00:00.0000000|1823|1164|
|2017-05-01 00:00:00.0000000|1811|1353|
|2017-05-08 00:00:00.0000000|1691|1246|
|2017-05-15 00:00:00.0000000|1812|1608|
|2017-05-22 00:00:00.0000000|1740|1017|
|2017-05-29 00:00:00.0000000|960|756|

:::image type="content" source="images/activity-metrics-plugin/activity-metrics-dcount-and-dcount-newvalues.png" border="false" alt-text="활동 메트릭 카운트 및 카운트 새 값":::
