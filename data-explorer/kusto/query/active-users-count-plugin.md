---
title: active_users_count 플러그 인-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 active_users_count 플러그 인을 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 22d3744cfa83a003830acc07710fd459003dbf20
ms.sourcegitcommit: 9fe6ee7db15a5cc92150d3eac0ee175f538953d2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2020
ms.locfileid: "82907195"
---
# <a name="active_users_count-plugin"></a>active_users_count 플러그 인

Lookback 기간의 최소 기간 이상에 각 값이 표시 되는 고유 값 수를 계산 합니다.

"팬이 아닌"의 모양새를 포함 하지 않고 "팬"의 고유 개수를 계산 하는 데 유용 합니다. 사용자는 lookback 기간 동안 활성화 된 경우에만 "팬"으로 계산 됩니다. Lookback 기간은 사용자가 ("팬")로 간주 `active` 되는지 여부를 결정 하는 데만 사용 됩니다. 집계 자체는 lookback 창의 사용자를 포함 하지 않습니다. 반면 [sliding_window_counts](sliding-window-counts-plugin.md) 집계는 lookback 기간의 슬라이딩 윈도우에 대해 수행 됩니다.

```kusto
T | evaluate active_users_count(id, datetime_column, startofday(ago(30d)), startofday(now()), 7d, 1d, 2, 7d, dim1, dim2, dim3)
```

**구문**

*T* `| evaluate` `,` *TimelineColumn* `,` *LookbackWindow* `,` *dim2* *End* `,` *Start* `,` *dim1* *IdColumn* `,` *Period* *ActivePeriodsCount* *Bin* idcolumn TimelineColumn`,` Start`,` End LookbackWindow Period`,` ActivePeriodsCount`,` Bin [dim1 dim2 ...] `active_users_count(``)`

**인수**

* *T*: 입력 테이블 형식 식입니다.
* *Idcolumn*: 사용자 활동을 나타내는 ID 값이 포함 된 열의 이름입니다. 
* *TimelineColumn*: 타임 라인을 나타내는 열의 이름입니다.
* *Start*: (선택 사항) 분석 시작 기간의 값을 포함 하는 스칼라입니다.
* *End*: (선택 사항) 분석 종료 기간의 값을 포함 하는 스칼라입니다.
* *LookbackWindow*: 사용자 모양을 확인 하는 기간을 정의 하는 슬라이딩 시간 창입니다. Lookback period는 ([current 모양새]-[Lookback])에서 시작 하 여 ([current 모양새])에 끝납니다. 
* *기간*: 단일 모양으로 계산 되는 스칼라 상수 timespan (사용자는이 timespan의 최소 고유 ActivePeriodsCount에 표시 되는 경우 활성으로 계산 됨)
* *ActivePeriodsCount*: 사용자가 활성 상태 인지 여부를 결정할 최소 고유 활성 기간 수입니다. 활성 사용자는 최소 (크거나 같음) 활성 기간 수에 나타난 사용자입니다.
* *Bin*: 분석 단계 기간의 스칼라 상수 값입니다. 는 숫자/날짜/시간/타임 스탬프 `week` / `month` / `year`값 이거나 인 문자열일 수 있습니다. 모든 기간은 해당 하는 [startofweek](startofweekfunction.md)/[startofmonth](startofmonthfunction.md)/[startofyear](startofyearfunction.md) 함수입니다.
* *dim1*, *dim2*, ...: (선택 사항) 활동 메트릭 계산을 분할 하는 차원 열의 목록입니다.

**반환**

ActivePeriodCounts에 표시 된 Id에 대 한 고유 카운트 값이 있는 테이블을 반환 합니다 .이는 lookback 기간, 각 타임 라인 기간 및 각 기존 차원 조합입니다.

출력 테이블 스키마는 다음과 같습니다.

|*TimelineColumn*|dim1|..|dim_n|dcount_values|
|---|---|---|---|---|
|유형: *TimelineColumn*|..|..|..|long|


**예**

지난 8 일 동안 3 일 이상 경과 된 개별 사용자 수를 계산 합니다. 분석 기간: 7 월 2018입니다.

```kusto
let Start = datetime(2018-07-01);
let End = datetime(2018-07-31);
let LookbackWindow = 8d;
let Period = 1d;
let ActivePeriods = 3;
let Bin = 7d; 
let T =  datatable(User:string, Timestamp:datetime)
[
    "B",      datetime(2018-06-29),
    "B",      datetime(2018-06-30),
    "A",      datetime(2018-07-02),
    "B",      datetime(2018-07-04),
    "B",      datetime(2018-07-08),
    "A",      datetime(2018-07-10),
    "A",      datetime(2018-07-14),
    "A",      datetime(2018-07-17),
    "A",      datetime(2018-07-20),
    "B",      datetime(2018-07-24)
]; 
T | evaluate active_users_count(User, Timestamp, Start, End, LookbackWindow, Period, ActivePeriods, Bin)



```

|타임스탬프|`dcount`|
|---|---|
|2018-07-01 00:00:00.0000000|1|
|2018-07-15 00:00:00.0000000|1|

사용자는 다음 조건 중 하나를 충족 하는 경우 활성 상태인 것으로 간주 됩니다. 
* 사용자가 3 개 이상의 고유 요일 (Period = 1d, ActivePeriods = 3)에 표시 되었습니다.
* 사용자가 8d의 lookback 창에 표시 되었으며 현재 모양을 포함 하 고 있습니다.

아래 그림에서이 조건에 의해 활성화 되는 유일한 모양은 다음과 같습니다. 사용자 A on 7/20 및 7/4의 사용자 B (위의 플러그 인 결과 참조) 사용자 B의 모양은 7/4에 lookback 창에 포함 되어 있지만 시작/종료 시간 범위 6/29-30에는 포함 되어 있지 않습니다. 

:::image type="content" source="images/queries/active-users-count.png" alt-text="활성 사용자 수 예":::
