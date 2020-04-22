---
title: activity_engagement 플러그인 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 activity_engagement 플러그인에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: a7ed7ad7ebef425160b64b792ff75c95d4c4fcee
ms.sourcegitcommit: 29018b3db4ea7d015b1afa65d49ecf918cdff3d6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2020
ms.locfileid: "82030326"
---
# <a name="activity_engagement-plugin"></a>activity_engagement 플러그 인

슬라이딩 타임라인 윈도우에서 ID 열을 기준으로 하여 활동 참여 비율을 계산합니다.

activity_engagement 플러그인은 DAU / WAU / MAU (일일 / 주간 / 월간 활동)를 계산하는 데 사용할 수 있습니다.

```kusto
T | evaluate activity_engagement(id, datetime_column, 1d, 30d)
```

**구문**

*T* `| evaluate` `,` `,` `,``,` `,` `,` *Start* `,` *TimelineColumn* `,` *dim2* *IdColumn* *End* *dim1* *OuterActivityWindow* *InnerActivityWindow* IdColumn 타임라인열 [ 시작 끝] 내부 활동창 외부 활동창 [ dim1 dim2 ...] `activity_engagement(``)`

**인수**

* *T*: 입력 테이블 형식 식입니다.
* IdColumn : 사용자 활동을 나타내는 ID 값이 있는 열의 *이름입니다.* 
* *타임라인열*: 타임라인을 나타내는 열의 이름입니다.
* *시작*: (선택 사항) 분석 시작 기간의 값을 가진 스칼라.
* *끝*: (선택 사항) 분석 종료 기간의 값을 가진 스칼라.
* *내부 활동창*: 내부 범위 분석 기간의 값을 가진 스칼라입니다.
* *외부 활동창*: 외부 범위 분석 기간의 값을 가진 스칼라입니다.
* *dim1*, *dim2*, ...: 활동 메트릭 계산을 슬라이스하는 차원 열의 (선택 사항) 목록입니다.

**반환**

각 내부 범위 창 기간 및 각 기존 차원 조합에 대해 (내부 범위 창 내의 ID 값의 고유 개수, 외부 범위 창 내의 ID 값 의 고유 개 수 및 활동 비율)가 있는 테이블을 반환합니다.

출력 테이블 스키마는 다음과 같은 것입니다.

|타임라인열|dcount_activities_inner|dcount_activities_outer|activity_ratio|어둡게 1|..|dim_n|
|---|---|---|---|--|--|--|--|--|--|
|유형: *타임라인열* 현재|long|long|double|..|..|..|


**예**

### <a name="dauwau-calculation"></a>DAU/WAU 계산

다음 예제에서는 임의로 생성된 데이터에 대해 DAU/WAU(일일 활성 사용자/주간 활성 사용자 비율)를 계산합니다.

```kusto
// Generate random data of user activities
let _start = datetime(2017-01-01);
let _end = datetime(2017-01-31);
range _day from _start to _end  step 1d
| extend d = tolong((_day - _start)/1d)
| extend r = rand()+1
| extend _users=range(tolong(d*50*r), tolong(d*50*r+100*r-1), 1) 
| mv-expand id=_users to typeof(long) limit 1000000
// Calculate DAU/WAU ratio
| evaluate activity_engagement(['id'], _day, _start, _end, 1d, 7d)
| project _day, Dau_Wau=activity_ratio*100 
| render timechart 
```

:::image type="content" source="images/activity-engagement-plugin/activity-engagement-dau-wau.png" border="false" alt-text="활동 참여 다우 와우":::

### <a name="daumau-calculation"></a>DAU/MAU 계산

다음 예제에서는 임의로 생성된 데이터에 대해 DAU/WAU(일일 활성 사용자/주간 활성 사용자 비율)를 계산합니다.

```kusto
// Generate random data of user activities
let _start = datetime(2017-01-01);
let _end = datetime(2017-05-31);
range _day from _start to _end  step 1d
| extend d = tolong((_day - _start)/1d)
| extend r = rand()+1
| extend _users=range(tolong(d*50*r), tolong(d*50*r+100*r-1), 1) 
| mv-expand id=_users to typeof(long) limit 1000000
// Calculate DAU/MAU ratio
| evaluate activity_engagement(['id'], _day, _start, _end, 1d, 30d)
| project _day, Dau_Mau=activity_ratio*100 
| render timechart 
```

:::image type="content" source="images/activity-engagement-plugin/activity-engagement-dau-mau.png" border="false" alt-text="활동 참여 다우 마우":::

### <a name="daumau-calculation-with-additional-dimensions"></a>추가 치수가 있는 DAU/MAU 계산

다음 예제에서는 추가 차원()을`mod3`사용하여 임의로 생성된 데이터에 대해 DAU/WAU(일일 활성 사용자/주간 활성 사용자 비율)를 계산합니다.

```kusto
// Generate random data of user activities
let _start = datetime(2017-01-01);
let _end = datetime(2017-05-31);
range _day from _start to _end  step 1d
| extend d = tolong((_day - _start)/1d)
| extend r = rand()+1
| extend _users=range(tolong(d*50*r), tolong(d*50*r+100*r-1), 1) 
| mv-expand id=_users to typeof(long) limit 1000000
| extend mod3 = strcat("mod3=", id % 3)
// Calculate DAU/MAU ratio
| evaluate activity_engagement(['id'], _day, _start, _end, 1d, 30d, mod3)
| project _day, Dau_Mau=activity_ratio*100, mod3 
| render timechart 
```

:::image type="content" source="images/activity-engagement-plugin/activity-engagement-dau-mau-mod3.png" border="false" alt-text="활동 참여 dau mau 모드 3":::
