---
title: active_users_count 플러그인 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 active_users_count 플러그인에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: f324507d1a4528c5efefc14f7820437383211ca6
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81519349"
---
# <a name="active_users_count-plugin"></a>active_users_count 플러그인

각 값이 룩백 기간의 최소 기간 수에 나타난 고유한 값 수를 계산합니다.

"팬이 아닌"의 출연을 포함하지 않는 동안 만 "팬"의 별개의 수를 계산하는 데 유용합니다. 사용자는 조회 기간 동안 활성 상태인 경우에만 "팬"으로 계산됩니다. 조회 기간은 사용자가 "팬"으로 간주되는지 `active` 여부를 결정하는 데만 사용됩니다. 집계 자체에는 회수 기간의 슬라이딩 창 위에 집계가 있는 [sliding_window_counts] (sliding-window-counts-plugin.md)와 달리 룩백 창의 사용자가 포함되지 않습니다.

```kusto
T | evaluate active_users_count(id, datetime_column, startofday(ago(30d)), startofday(now()), 7d, 1d, 2, 7d, dim1, dim2, dim3)
```

**구문**

*T* `| evaluate` `,` *Start* `,` *End* `,` `,` *Period* `,` `,` *Bin* `,` `,` `,` *IdColumn* `,` *TimelineColumn* *LookbackWindow* *dim2* *dim1* *ActivePeriodsCount* IdColumn 타임라인열 시작 끝 뒤로창 기간 활성기간계산 빈 [dim1 dim2 ...] `active_users_count(``)`

**인수**

* *T*: 입력 테이블 형식 식입니다.
* IdColumn : 사용자 활동을 나타내는 ID 값이 있는 열의 *이름입니다.* 
* *타임라인열*: 타임라인을 나타내는 열의 이름입니다.
* *시작*: (선택 사항) 분석 시작 기간의 값을 가진 스칼라.
* *끝*: (선택 사항) 분석 종료 기간의 값을 가진 스칼라.
* *Lookback창*: 사용자 모양이 검사되는 기간을 정의하는 슬라이딩 시간 창입니다. 조회 기간은 [현재 모양] - [뒤로 창]에서 시작하여 ([현재 모양])에서 끝납니다. 
* *기간*: 스칼라 상수 시간 범위가 단일 모양으로 계산됩니다(이 시간 범위의 최소 고유 ActivePeriodsCount에 나타나는 경우 사용자는 활성으로 계산됩니다.
* *ActivePeriodsCount*: 사용자가 활성 상태인지 여부를 결정하기 위해 최소 개의 고유 활성 기간입니다. 활성 사용자는 활성 기간 수에 적어도 (같거나 그 이상)에 나타난 사용자입니다.
* *Bin*: 분석 단계 기간의 스칼라 상수 값입니다. 숫자 /날짜 시간 / 타임 스탬프 값 또는 중 하나인 `week` / `month` / `year`문자열일 수 있으며, 이 경우 모든 마침표는 그에 따라 [startofweek](startofweekfunction.md)/[startofmonth](startofmonthfunction.md)/[startofyear가](startofyearfunction.md) 됩니다.
* *dim1*, *dim2*, ...: 활동 메트릭 계산을 슬라이스하는 차원 열의 (선택 사항) 목록입니다.

**반환**

조회 기간, 각 타임라인 기간 및 각 기존 차원 조합에 대해 ActivePeriodCounts를 통해 나타난 ID에 대한 고유 개수 값이 있는 테이블을 반환합니다.

출력 테이블 스키마는 다음과 같은 것입니다.

|*타임라인열*|어둡게 1|..|dim_n|dcount_values|
|---|---|---|---|---|
|유형: *타임라인열* 현재|..|..|..|long|


**예**

지난 8일 동안 적어도 3일 동안 나타난 고유 사용자의 주간 수를 계산합니다. 분석 기간: 2018년 7월.

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

|타임스탬프|dcount|
|---|---|
|2018-07-01 00:00:00.0000000|1|
|2018-07-15 00:00:00.0000000|1|

사용자는 현재 모양(현재 모양 포함)보다 이전 8d의 룩백 창에서 최소 3일(기간 = 1d, ActivePeriods=3)에서 보인 경우 활성 으로 간주됩니다. 아래 그림에서 이 기준에 따라 활성화된 유일한 모양은 7/20의 사용자 A와 7/4의 사용자 B입니다(위의 플러그인 결과 참조). 6/29-30의 사용자 B의 모양은 시작 종료 시간 범위에 없지만 7/4의 사용자 B의 룩백 창에 포함됩니다. 

![대체 텍스트](images/queries/active-users-count.png "활성 사용자 수")