---
title: activity_counts_metrics 플러그 인-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 activity_counts_metrics 플러그 인을 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: b06b1c137552ba19f9b1ef5367a25bb72eea5c93
ms.sourcegitcommit: 4f68d6dbfa6463dbb284de0aa17fc193d529ce3a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82742037"
---
# <a name="activity_counts_metrics-plugin"></a>activity_counts_metrics 플러그 인

이전 시간 창에 비교/집계 된 각 *기간에 대* 한 유용한 활동 메트릭을 계산 합니다. 메트릭에는 총 개수 값, 고유 카운트 값, 새 값의 고유 카운트 및 집계 된 고유 개수가 포함 됩니다. 이 플러그 인을 [activity_metrics 플러그 인](activity-metrics-plugin.md)과 비교 하 여 모든 기간을 이전 시간 창과 비교할 수 있습니다.

```kusto
T | evaluate activity_counts_metrics(id, datetime_column, startofday(ago(30d)), startofday(now()), 1d, dim1, dim2, dim3)
```

**구문**

*T* `| evaluate` `,` *TimelineColumn* `,` *dim1* *Start* `,` *dim2* *Window* `,` *IdColumn* `,` *End* *Cohort*idcolumn`,` TimelineColumn Start End`,` Window [`,` 코 호트] [dim1 dim2] `activity_counts_metrics(` [`,` *Lookback*]`)`

**인수**

* *T*: 입력 테이블 형식 식입니다.
* *Idcolumn*: 사용자 활동을 나타내는 ID 값이 포함 된 열의 이름입니다. 
* *TimelineColumn*: 타임 라인을 나타내는 열의 이름입니다.
* *Start*: 분석 시작 기간의 값을 포함 하는 스칼라입니다.
* *종료*: 분석 종료 기간의 값을 포함 하는 스칼라입니다.
* *Window*: 분석 기간 값이 포함 된 스칼라입니다. 는 `week` / `month` / `year`숫자/날짜/시간/타임 스탬프 값 이거나 중 하나인 문자열일 수 있으며,이 경우 모든 기간이 [startofweek](startofweekfunction.md)/[startofmonth](startofmonthfunction.md) 또는 [startofyear](startofyearfunction.md)가 됩니다. 
* *dim1*, *dim2*, ...: (선택 사항) 활동 메트릭 계산을 분할 하는 차원 열의 목록입니다.

**반환**

는 총 개수 값, 고유 카운트 값, 새 값의 고유 개수 및 각 기간에 대 한 집계 된 고유 카운트를 포함 하는 테이블을 반환 합니다.

출력 테이블 스키마는 다음과 같습니다.

|`TimelineColumn`|`dim1`|...|`dim_n`|`count`|`dcount`|`new_dcount`|`aggregated_dcount`
|---|---|---|---|---|---|---|---|---|
|형식: 다음으로*`TimelineColumn`*|..|..|..|long|long|long|long|long


* *`TimelineColumn`*: 시간 창 시작 시간입니다.
* *`count`*: 시간 창 및 *dim* 의 총 레코드 수
* *`dcount`*: 시간 창과 *dim* 의 고유 ID 값 수
* *`new_dcount`*: 모든 이전 시간 창과 비교 하 여 시간 창과 *dim* 의 고유 ID 값입니다. 
* *`aggregated_dcount`*: 첫 번째 창에서 현재 (포함)까지 *dim* 의 총 집계 된 고유 ID 값입니다.

**예**

### <a name="daily-activity-counts"></a>일별 활동 수 

다음 쿼리는 제공 된 입력 테이블의 일별 활동 수를 계산 합니다.

```kusto
let start=datetime(2017-08-01);
let end=datetime(2017-08-04);
let window=1d;
let T = datatable(UserId:string, Timestamp:datetime)
[
'A', datetime(2017-08-01),
'D', datetime(2017-08-01), 
'J', datetime(2017-08-01),
'B', datetime(2017-08-01),
'C', datetime(2017-08-02),  
'T', datetime(2017-08-02),
'J', datetime(2017-08-02),
'H', datetime(2017-08-03),
'T', datetime(2017-08-03),
'T', datetime(2017-08-03),
'J', datetime(2017-08-03),
'B', datetime(2017-08-03),
'S', datetime(2017-08-03),
'S', datetime(2017-08-04),
];
 T 
 | evaluate activity_counts_metrics(UserId, Timestamp, start, end, window)
```

|`Timestamp`|`count`|`dcount`|`new_dcount`|`aggregated_dcount`|
|---|---|---|---|---|
|2017-08-01 00:00:00.0000000|4|4|4|4|
|2017-08-02 00:00:00.0000000|3|3|2|6|
|2017-08-03 00:00:00.0000000|6|5|2|8|
|2017-08-04 00:00:00.0000000|1|1|0|8|


