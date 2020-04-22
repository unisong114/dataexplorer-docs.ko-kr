---
title: session_count 플러그인 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 session_count 플러그인에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 76ce6ca3be1859aba0a94ae155c60342be3f1ad1
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663146"
---
# <a name="session_count-plugin"></a>session_count 플러그 인

타임라인을 통해 ID 열을 기준으로 세션 수를 계산합니다.

```kusto
T | evaluate session_count(id, datetime_column, startofday(ago(30d)), startofday(now()), 1min, 30min, dim1, dim2, dim3)
```

**구문**

*T* `| evaluate` `,` *Start* `,` *End* `,` *Bin* `,` `,` `,` `,` *IdColumn* `,` *TimelineColumn* *dim1* *dim2* *LookBackWindow* IdColumn 타임라인열 시작 끝 빈 룩백윈도우 [ dim1 dim2 ...] `session_count(``)`

**인수**

* *T*: 입력 테이블 형식 식입니다.
* IdColumn : 사용자 활동을 나타내는 ID 값이 있는 열의 *이름입니다.* 
* *타임라인열*: 타임라인을 나타내는 열의 이름입니다.
* *시작*: 분석 시작 기간의 값을 가진 스칼라.
* *끝*: 분석 종료 기간의 값을 가진 스칼라.
* *빈*: 세션 분석 단계 기간의 스칼라 상수 값.
* *LookBackWindow*: 세션 회수 기간을 나타내는 스칼라 상수 값입니다. 에서 `IdColumn` ID가 내의 `LookBackWindow` 시간 창에 나타나는 경우 - 세션이 기존으로 간주됩니다(그렇지 않은 경우) 세션이 새 것으로 간주됩니다.
* *dim1*, *dim2*, ...: (선택 사항) 세션 수 계산을 슬라이스 차원 열의 목록입니다.

**반환**

각 타임라인 기간 및 각 기존 차원 조합에 대한 세션 수 값이 있는 테이블을 반환합니다.

출력 테이블 스키마는 다음과 같은 것입니다.

|*타임라인열*|어둡게 1|..|dim_n|count_sessions|
|---|---|---|---|---|--|--|--|--|--|--|
|유형: *타임라인열* 현재|..|..|..|long|


**예**


예제를 위해 두 개의 열이 있는 테이블인 데이터를 결정적으로 만듭니다.
- 타임라인: 1에서 10,000까지의 실행 번호
- ID: 사용자의 ID가 1에서 50까지입니다.

`Id`의 `Timeline` `Timeline` 구분자인 경우 특정 슬롯에 나타납니다(타임라인 % ID == 0).

즉, 이벤트가 `Id==1` 모든 `Timeline` 슬롯에 표시되고, `Id==2` 모든 두 `Timeline` 번째 슬롯에 이벤트가 표시됩니다.

다음은 데이터의 몇 가지 20 줄입니다.

```kusto
let _data = range Timeline from 1 to 10000 step 1
| extend __key = 1
| join kind=inner (range Id from 1 to 50 step 1 | extend __key=1) on __key
| where Timeline % Id == 0
| project Timeline, Id;
// Look on few lines of the data
_data
| order by Timeline asc, Id asc
| limit 20
```

|타임라인|Id|
|---|---|
|1|1|
|2|1|
|2|2|
|3|1|
|3|3|
|4|1|
|4|2|
|4|4|
|5|1|
|5|5|
|6|1|
|6|2|
|6|3|
|6|6|
|7|1|
|7|7|
|8|1|
|8|2|
|8|4|
|8|8|

다음 용어로 세션을 정의해 보겠습니다: 사용자 ()`Id`가 100개의 시간 슬롯의 시간 프레임에 적어도 한 번 나타나면 활성으로 간주되는 세션, 세션 룩백 창은 41개의 시간 슬롯입니다.

다음 쿼리는 위의 정의에 따라 활성 세션의 수를 보여 줍니다.

```kusto
let _data = range Timeline from 1 to 9999 step 1
| extend __key = 1
| join kind=inner (range Id from 1 to 50 step 1 | extend __key=1) on __key
| where Timeline % Id == 0
| project Timeline, Id;
// End of data definition
_data
| evaluate session_count(Id, Timeline, 1, 10000, 100, 41)
| render linechart 
```

:::image type="content" source="images/queries/example-session-count.png" alt-text="세션 수 예제":::
