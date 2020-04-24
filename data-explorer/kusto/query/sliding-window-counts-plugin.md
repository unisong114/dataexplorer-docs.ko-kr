---
title: sliding_window_counts 플러그인 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 sliding_window_counts 플러그인에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: feab3d0e8f548817be12f202eb2d494bd65aa133
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81507500"
---
# <a name="sliding_window_counts-plugin"></a>sliding_window_counts 플러그인

여기에 설명된 기술을 사용하여 뒤로 하는 기간 동안 슬라이딩 창에서 값의 개수와 고유 수를 [계산합니다.](samples.md#performing-aggregations-over-a-sliding-window)

예를 들어, *매일,* 지난 *주에*사용자의 수와 고유 수를 계산합니다. 

```kusto
T | evaluate sliding_window_counts(id, datetime_column, startofday(ago(30d)), startofday(now()), 7d, 1d, dim1, dim2, dim3)
```

**구문**

*T* `| evaluate` `,` *End* `,` *LookbackWindow* `,` `,` `,` `,` *IdColumn* `,` `,` *Start* *dim2* *TimelineColumn* *Bin* *dim1* IdColumn 타임라인열 시작 끝 뒤로 창 [dim1 dim2 ...] `sliding_window_counts(``)`

**인수**

* *T*: 입력 테이블 형식 식입니다.
* IdColumn : 사용자 활동을 나타내는 ID 값이 있는 열의 *이름입니다.* 
* *타임라인열*: 타임라인을 나타내는 열의 이름입니다.
* *시작*: 분석 시작 기간의 값을 가진 스칼라.
* *끝*: 분석 종료 기간의 값을 가진 스칼라.
* *LookbackWindow*: 룩백 기간의 스칼라 상수 값(예: 지난 7d의 dcount 사용자: LookbackWindow = 7d)
* *Bin*: 분석 단계 기간의 스칼라 불변 값입니다. 숫자 /날짜 시간 / 타임 스탬프 값 또는 중 하나인 `week` / `month` / `year`문자열일 수 있으며, 이 경우 모든 마침표는 그에 따라 [startofweek](startofweekfunction.md)/[startofmonth](startofmonthfunction.md)/[startofyear가](startofyearfunction.md) 됩니다. 
* *dim1*, *dim2*, ...: 활동 메트릭 계산을 슬라이스하는 차원 열의 (선택 사항) 목록입니다.

**반환**

룩백 기간, 각 타임라인 기간(bin) 및 각 기존 차원 조합에 대해 Id의 개수 및 고유 개수 값이 있는 테이블을 반환합니다.

출력 테이블 스키마는 다음과 같은 것입니다.

|*타임라인열*|어둡게 1|..|dim_n|개수|카운트 (것)수|
|---|---|---|---|---|---|
|유형: *타임라인열* 현재|..|..|..|long|long|


**예**

분석 기간의 매일 지난 주에 사용자에 대한 수와 계산을 계산합니다. 

```kusto
let start = datetime(2017 - 08 - 01);
let end = datetime(2017 - 08 - 07); 
let lookbackWindow = 3d;  
let bin = 1d;
let T = datatable(UserId:string, Timestamp:datetime)
[
'Bob',      datetime(2017 - 08 - 01), 
'David',    datetime(2017 - 08 - 01), 
'David',    datetime(2017 - 08 - 01), 
'John',     datetime(2017 - 08 - 01), 
'Bob',      datetime(2017 - 08 - 01), 
'Ananda',   datetime(2017 - 08 - 02),  
'Atul',     datetime(2017 - 08 - 02), 
'John',     datetime(2017 - 08 - 02), 
'Ananda',   datetime(2017 - 08 - 03), 
'Atul',     datetime(2017 - 08 - 03), 
'Atul',     datetime(2017 - 08 - 03), 
'John',     datetime(2017 - 08 - 03), 
'Bob',      datetime(2017 - 08 - 03), 
'Betsy',    datetime(2017 - 08 - 04), 
'Bob',      datetime(2017 - 08 - 05), 
];
T | evaluate sliding_window_counts(UserId, Timestamp, start, end, lookbackWindow, bin)


```

|타임스탬프|개수|카운트 (것)수|
|---|---|---|
|2017-08-01 00:00:00.0000000|5|3|
|2017-08-02 00:00:00.0000000|8|5|
|2017-08-03 00:00:00.0000000|13|5|
|2017-08-04 00:00:00.0000000|9|5|
|2017-08-05 00:00:00.0000000|7|5|
|2017-08-06 00:00:00.0000000|2|2|
|2017-08-07 00:00:00.0000000|1|1|