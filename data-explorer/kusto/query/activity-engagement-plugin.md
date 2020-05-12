---
title: activity_engagement 플러그 인-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 activity_engagement 플러그 인을 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 9aa85bcb12cd5f8d836f58ea9d16a318d8a40506
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83225958"
---
# <a name="activity_engagement-plugin"></a>activity_engagement 플러그 인

슬라이딩 타임라인 윈도우에서 ID 열을 기준으로 하여 활동 참여 비율을 계산합니다.

activity_engagement 플러그 인은 6AU/WAU/MAU (일별/주별/월별 활동)를 계산 하는 데 사용할 수 있습니다.

```kusto
T | evaluate activity_engagement(id, datetime_column, 1d, 30d)
```

**구문**

*T* `| evaluate` `activity_engagement(` *idcolumn* `,` *TimelineColumn* `,` [*Start* `,` *End* `,` ] *inneractivitywindow* `,` *OuterActivityWindow* [ `,` *dim1* `,` *dim2* `,` ...]`)`

**인수**

* *T*: 입력 테이블 형식 식입니다.
* *Idcolumn*: 사용자 활동을 나타내는 ID 값이 포함 된 열의 이름입니다. 
* *TimelineColumn*: 타임 라인을 나타내는 열의 이름입니다.
* *Start*: (선택 사항) 분석 시작 기간의 값을 포함 하는 스칼라입니다.
* *End*: (선택 사항) 분석 종료 기간의 값을 포함 하는 스칼라입니다.
* *Inneractivitywindow*: 내부 범위 분석 기간 값을 포함 하는 스칼라입니다.
* *OuterActivityWindow*: 외부 범위 분석 기간 값을 포함 하는 스칼라입니다.
* *dim1*, *dim2*, ...: (선택 사항) 활동 메트릭 계산을 분할 하는 차원 열의 목록입니다.

**반환**

각 내부 범위 창 기간 및 각 기존 차원 조합에 대해 (내부 범위 창 내에서 ID 값의 고유 개수, 외부 범위 창 내에서 고유한 ID 값의 개수 및 작업 비율)가 있는 테이블을 반환 합니다.

출력 테이블 스키마는 다음과 같습니다.

|TimelineColumn|dcount_activities_inner|dcount_activities_outer|activity_ratio|dim1|..|dim_n|
|---|---|---|---|--|--|--|--|--|--|
|유형: *TimelineColumn*|long|long|double|..|..|..|


**예**

### <a name="dauwau-calculation"></a>WAU 계산

다음 예에서는 무작위로 생성 된 데이터에 대해 6AU/WAU (일별 활성 사용자/주간 활성 사용자 비율)을 계산 합니다.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
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

:::image type="content" source="images/activity-engagement-plugin/activity-engagement-dau-wau.png" border="false" alt-text="활동 참여 wau":::

### <a name="daumau-calculation"></a>6AU/MAU 계산

다음 예에서는 무작위로 생성 된 데이터에 대해 6AU/WAU (일별 활성 사용자/주간 활성 사용자 비율)을 계산 합니다.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
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

:::image type="content" source="images/activity-engagement-plugin/activity-engagement-dau-mau.png" border="false" alt-text="활동 참여 (& u)":::

### <a name="daumau-calculation-with-additional-dimensions"></a>추가 차원이 포함 된 AAU/MAU 계산

다음 예에서는 추가 차원 ()을 사용 하 여 임의로 생성 된 데이터에 대해 6AU/WAU (일별 활성 사용자/주간 활성 사용자 비율)를 계산 합니다 `mod3` .

<!-- csl: https://help.kusto.windows.net:443/Samples -->
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

:::image type="content" source="images/activity-engagement-plugin/activity-engagement-dau-mau-mod3.png" border="false" alt-text="활동 참여-au mau mod 3":::
