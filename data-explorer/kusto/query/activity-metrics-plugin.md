---
title: activity_metrics 플러그 인-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기에서 activity_metrics 플러그 인을 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 8106d419f20dcacdec6386294a5b9ffb8d1bc8e2
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83225907"
---
# <a name="activity_metrics-plugin"></a>activity_metrics 플러그 인

현재 기간 창과 이전 기간을 기반 *으로 하는* 유용한 활동 메트릭 (고유 카운트 값, 새 값의 고유 개수, 보존 률 및 변동 율)을 계산 합니다 .이는 모든 시간 창이 이전 시간 창과 비교할 [activity_counts_metrics 플러그 인](activity-counts-metrics-plugin.md) 과는 다릅니다.

```kusto
T | evaluate activity_metrics(id, datetime_column, startofday(ago(30d)), startofday(now()), 1d, dim1, dim2, dim3)
```

**구문**

*T* `| evaluate` `activity_metrics(` *idcolumn* `,` *TimelineColumn* `,` [*Start* `,` *End* `,` ] *Window* [ `,` *dim1* `,` *dim2* `,` ...]`)`

**인수**

* *T*: 입력 테이블 형식 식입니다.
* *Idcolumn*: 사용자 활동을 나타내는 ID 값이 포함 된 열의 이름입니다. 
* *TimelineColumn*: 타임 라인을 나타내는 열의 이름입니다.
* *Start*: (선택 사항) 분석 시작 기간의 값을 포함 하는 스칼라입니다.
* *End*: (선택 사항) 분석 종료 기간의 값을 포함 하는 스칼라입니다.
* *Window*: 분석 기간 값이 포함 된 스칼라입니다. 는 숫자/날짜/시간/타임 스탬프 값 또는 중 하나인 문자열일 수 있으며,이 `week` / `month` / `year` 경우 모든 기간이 적절 한 [startofweek](startofweekfunction.md) / [startofmonth](startofmonthfunction.md) / [startofyear](startofyearfunction.md) 됩니다. 
* *dim1*, *dim2*, ...: (선택 사항) 활동 메트릭 계산을 분할 하는 차원 열의 목록입니다.

**반환**

각 시간 표시 막대와 각 기존 차원 조합에 대해 고유 카운트 값, 고유 개수의 새 값, 보존 률 및 변동 률을 포함 하는 테이블을 반환 합니다.

출력 테이블 스키마는 다음과 같습니다.

|*TimelineColumn*|dcount_values|dcount_newvalues|retention_rate|churn_rate|dim1|..|dim_n|
|---|---|---|---|---|--|--|--|--|--|--|
|유형: *TimelineColumn*|long|long|double|double|..|..|..|

**참고 사항**

***보존 률 정의***

`Retention Rate`일정 기간 동안 다음과 같이 계산 됩니다.

    # of customers returned during the period
    / (divided by)
    # customers at the beginning of the period

여기서는 `# of customers returned during the period` 다음과 같이 정의 됩니다.

    # of customers at end of period
    - (minus)
    # of new customers acquired during the period

`Retention Rate`0.0에서 1.0까지 달라질 수 있습니다.  
점수를 높게 설정할수록 반환 되는 사용자의 수가 커집니다.


***변동 율 정의***

`Churn Rate`일정 기간 동안 다음과 같이 계산 됩니다.
    
    # of customers lost in the period
    / (divided by)
    # of customers at the beginning of the period

여기서는 `# of customer lost in the period` 다음과 같이 정의 됩니다.

    # of customers at the beginning of the period
    - (minus)
    # of customers at the end of the period

`Churn Rate`0.0에서 1.0까지 달라질 수 있습니다. 점수가 높으면 더 많은 사용자가 서비스에 반환 되지 않는 것을 의미 합니다.

***변동 대비 및 보존 률***

및의 정의에서 파생 `Churn Rate` 되는 경우 `Retention Rate` 다음은 항상 true입니다.

    [Retention rate] = 100.0% - [Churn Rate]


**예**

### <a name="weekly-retention-rate-and-churn-rate"></a>주간 보존 률 및 변동 율

다음 쿼리는 주-주 기간의 보존 및 변동 율을 계산 합니다.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
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

:::image type="content" source="images/activity-metrics-plugin/activity-metrics-churn-and-retention.png" border="false" alt-text="활동 메트릭 변동 및 보존":::

### <a name="distinct-values-and-distinct-new-values"></a>고유 값 및 고유 ' 새 ' 값 

다음 쿼리에서는 주-주 창에 대 한 고유 값과 ' 새 ' 값 (이전 기간에 표시 되지 않은 id)을 계산 합니다.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
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

:::image type="content" source="images/activity-metrics-plugin/activity-metrics-dcount-and-dcount-newvalues.png" border="false" alt-text="활동 메트릭은 새 값을 만들고 dcount 합니다.":::
