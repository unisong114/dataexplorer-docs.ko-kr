---
title: activity_counts_metrics 플러그인 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 activity_counts_metrics 플러그인에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: e55cd940850440499d227082f57769e499e6a551
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81519315"
---
# <a name="activity_counts_metrics-plugin"></a>activity_counts_metrics 플러그 인

[Activity_metrics 플러그 인](activity-metrics-plugin.md)과 달리, 이전 시간 창과 비교/집계 된 각 시간 창에 대한 유용한 활동 메트릭 (총 개수 값, 고유 카운트 값, 고유 개수의 새 값, 집계 된 고유 카운트)을 계산 합니다. *모든* 기간을 이전 시간 창과 비교할 때만 해당 됩니다.

```kusto
T | evaluate activity_counts_metrics(id, datetime_column, startofday(ago(30d)), startofday(now()), 1d, dim1, dim2, dim3)
```

**구문**

*T* `| evaluate` `,` `,` *End* `,` *Window* `,` `,` `,` `,` *IdColumn* `,` *Start* *TimelineColumn* *dim1* *dim2* *Cohort*IdColumn 타임라인열 시작 창 [ 코호트] [ dim1 dim2 ...] `activity_counts_metrics(` [`,` *뒤로*]`)`

**인수**

* *T*: 입력 테이블 형식 식입니다.
* IdColumn : 사용자 활동을 나타내는 ID 값이 있는 열의 *이름입니다.* 
* *타임라인열*: 타임라인을 나타내는 열의 이름입니다.
* *시작*: 분석 시작 기간의 값을 가진 스칼라.
* *끝*: 분석 종료 기간의 값을 가진 스칼라.
* *창*: 분석 창 기간의 값을 가진 스칼라. 숫자 /날짜 시간 / 타임 스탬프 값 또는 중 하나인 `week` / `month` / `year`문자열일 수 있으며, 이 경우 모든 마침표는 그에 따라 [startofweek](startofweekfunction.md)/[startofmonth](startofmonthfunction.md)/[startofyear가](startofyearfunction.md) 됩니다. 
* *dim1*, *dim2*, ...: 활동 메트릭 계산을 슬라이스하는 차원 열의 (선택 사항) 목록입니다.

**반환**

총 개수 값, 고유 개 수 값, 새 값의 고유 개수, 각 시간 창에 대해 집계된 고유 개수가 있는 테이블을 반환합니다.

출력 테이블 스키마는 다음과 같은 것입니다.

|타임라인열|어둡게 1|...|dim_n|count|dcount|new_dcount|aggregated_dcount
|---|---|---|---|---|---|---|---|---|
|유형: *타임라인열* 현재|..|..|..|long|long|long|long|long


* *타임라인열*: 시간 창 시작 시간입니다.
* *개수*: 시간 창의 총 레코드 수 및 *흐리게 표시됩니다.*
* *countcount*: 시간 창에서 고유 ID 값이 계산되고 *흐리게 표시됩니다.*
* *new_dcount*: 시간 창의 고유 ID 값과 이전 의 모든 시간 창과 비교하여 *흐리게* 표시됩니다. 
* *aggregated_dcount*: 1시간 창에서 현재(포함)까지의 총 집계된 *고유* ID 값입니다.

**예**

### <a name="daily-activity-counts"></a>일일 활동 수 

다음 쿼리는 제공된 입력 테이블에 대한 일일 활동 수를 계산합니다.

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

|타임스탬프|count|dcount|new_dcount|aggregated_dcount|
|---|---|---|---|---|
|2017-08-01 00:00:00.0000000|4|4|4|4|
|2017-08-02 00:00:00.0000000|3|3|2|6|
|2017-08-03 00:00:00.0000000|6|5|2|8|
|2017-08-04 00:00:00.0000000|1|1|0|8|


