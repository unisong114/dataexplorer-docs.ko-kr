---
title: session_count 플러그 인-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 session_count 플러그 인을 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: c46430fe7acc75685b90d2322d709392c91ed6dc
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87351219"
---
# <a name="session_count-plugin"></a>session_count 플러그 인

타임 라인에 대 한 ID 열을 기반으로 세션 수를 계산 합니다.

```kusto
T | evaluate session_count(id, datetime_column, startofday(ago(30d)), startofday(now()), 1min, 30min, dim1, dim2, dim3)
```

## <a name="syntax"></a>구문

*T* `| evaluate` `session_count(` *idcolumn* `,` *TimelineColumn* `,` *Start* `,` *End* `,` *Bin* `,` *LookBackWindow* [ `,` *dim1* `,` *dim2* `,` ...]`)`

## <a name="arguments"></a>인수

* *T*: 입력 테이블 형식 식입니다.
* *Idcolumn*: 사용자 활동을 나타내는 ID 값이 포함 된 열의 이름입니다. 
* *TimelineColumn*: 타임 라인을 나타내는 열의 이름입니다.
* *Start*: 분석 시작 기간의 값을 포함 하는 스칼라입니다.
* *종료*: 분석 종료 기간의 값을 포함 하는 스칼라입니다.
* *Bin*: 세션 분석 단계 기간의 스칼라 상수 값입니다.
* *LookBackWindow*: session lookback period를 나타내는 스칼라 상수 값입니다. 의 ID가 `IdColumn` 내에서 기간에 표시 되는 경우 `LookBackWindow` 세션은 기존 항목으로 간주 됩니다. ID가 표시 되지 않으면 세션이 새로운 것으로 간주 됩니다.
* *dim1*, *dim2*, ...: (선택 사항) 세션 수 계산을 조각화 하는 차원 열의 목록입니다.

## <a name="returns"></a>반환

각 타임 라인 기간 및 각 기존 차원 조합에 대 한 세션 수 값이 있는 테이블을 반환 합니다.

출력 테이블 스키마는 다음과 같습니다.

|*TimelineColumn*|dim1|..|dim_n|count_sessions|
|---|---|---|---|---|--|--|--|--|--|--|
|유형: *TimelineColumn*|..|..|..|long|


## <a name="examples"></a>예

이 예에서는 데이터가 결정적 이며 두 개의 열이 있는 테이블을 사용 합니다.
- 타임 라인: 1에서 1만 사이의 실행 수
- Id: 사용자의 Id는 1 ~ 50입니다.

`Id`는 `Timeline` `Timeline` (타임 라인% Id = = 0)의 구분선 인 경우 특정 슬롯에 표시 됩니다.

가 있는 이벤트는 `Id==1` 모든 `Timeline` 슬롯, 모든 `Id==2` 초 슬롯에 있는 이벤트 등에 표시 됩니다 `Timeline` .

데이터의 20 줄은 다음과 같습니다.

<!-- csl: https://help.kusto.windows.net/Samples -->
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

사용자 ( `Id` )가 최소 100 시간 슬롯에서 한 번 이상 표시 되는 반면 세션 조회 창은 41 시간 슬롯에 있는 경우 세션을 다음 용어로 정의 하겠습니다.

다음 쿼리에서는 위의 정의에 따라 활성 세션 수를 보여 줍니다.

<!-- csl: https://help.kusto.windows.net/Samples -->
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

:::image type="content" source="images/session-count-plugin/example-session-count.png" alt-text="예제 세션 수" border="false":::
