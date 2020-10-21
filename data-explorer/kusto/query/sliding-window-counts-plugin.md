---
title: sliding_window_counts 플러그 인-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 sliding_window_counts 플러그 인을 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 9234745a002b88acf23b2e177aa8bc11cda45857
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92241806"
---
# <a name="sliding_window_counts-plugin"></a>sliding_window_counts 플러그 인

[여기](samples.md#perform-aggregations-over-a-sliding-window)에 설명 된 기술을 사용 하 여 lookback 기간 동안 슬라이딩 윈도우에서 개수 및 고유 값 수를 계산 합니다.

예를 들어 *매일*에 대해 이전 *주*에 사용자의 수 및 고유 수를 계산 합니다. 

```kusto
T | evaluate sliding_window_counts(id, datetime_column, startofday(ago(30d)), startofday(now()), 7d, 1d, dim1, dim2, dim3)
```

## <a name="syntax"></a>구문

*T* `| evaluate` `sliding_window_counts(` *idcolumn* `,` *TimelineColumn* `,` *Start* `,` *End* `,` *LookbackWindow* `,` *Bin* `,` [*dim1* `,` *dim2* `,` ...]`)`

## <a name="arguments"></a>인수

* *T*: 입력 테이블 형식 식입니다.
* *Idcolumn*: 사용자 활동을 나타내는 ID 값이 포함 된 열의 이름입니다. 
* *TimelineColumn*: 타임 라인을 나타내는 열의 이름입니다.
* *Start*: 분석 시작 기간의 값을 포함 하는 스칼라입니다.
* *종료*: 분석 종료 기간의 값을 포함 하는 스칼라입니다.
* *LookbackWindow*: lookback 기간의 스칼라 상수 값 (예 `dcount` : 지난 7d: LookbackWindow = 7d)
* *Bin*: 분석 단계 기간의 스칼라 상수 값입니다. 이 값은 숫자/날짜/시간/타임 스탬프 값일 수 있습니다. 값이 형식의 문자열이 면 `week` / `month` / `year` 모든 기간이 [startofweek](startofweekfunction.md) / [startofmonth](startofmonthfunction.md) / [startofyear](startofyearfunction.md)가 됩니다. 
* *dim1*, *dim2*, ...: (선택 사항) 활동 메트릭 계산을 분할 하는 차원 열의 목록입니다.

## <a name="returns"></a>반환

각 타임 라인 기간 (bin) 및 각 기존 차원 조합에 대해 lookback 기간에 Id의 개수 및 고유 카운트 값이 있는 테이블을 반환 합니다.

출력 테이블 스키마는 다음과 같습니다.

|*TimelineColumn*|`dim1`|..|`dim_n`|`count`|`dcount`|
|---|---|---|---|---|---|
|유형: *TimelineColumn*|..|..|..|long|long|


## <a name="examples"></a>예

`dcounts`지난 주에 분석 기간의 각 날짜에 대 한 개수와 사용자의 수를 계산 합니다. 

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

|타임스탬프|개수|`dcount`|
|---|---|---|
|2017-08-01 00:00:00.0000000|5|3|
|2017-08-02 00:00:00.0000000|8|5|
|2017-08-03 00:00:00.0000000|13|5|
|2017-08-04 00:00:00.0000000|9|5|
|2017-08-05 00:00:00.0000000|7|5|
|2017-08-06 00:00:00.0000000|2|2|
|2017-08-07 00:00:00.0000000|1|1|