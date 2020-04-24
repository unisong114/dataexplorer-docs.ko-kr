---
title: 시간 내에 가입 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 시간 기간 내에 조인에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: aa3b81694714ef5af94407cdfdac263af0631e40
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81513365"
---
# <a name="joining-within-time-window"></a>시간 내에 가입

일부 높은 카디널리티 키(예: 작업 ID 또는 세션 ID)의 두 개의 큰 데이터 집합 간에 조인하고 왼쪽과 오른쪽의 열 사이의`$right``$left` `datetime` "시간 거리"에 제한을 추가하여 각 왼쪽() 레코드에 대해 일치해야 하는 오른쪽 () 레코드를 추가로 제한하는 것이 유용합니다. 이는 일반적인 Kusto 조인 작업과 는 달리 "equi-join" 부분(하이 카디널리티 키 또는 왼쪽 및 오른쪽 데이터 세트일치)에 더하여, 시스템은 거리 함수를 적용하여 조인 속도를 상당히 높일 수 있습니다. 거리 함수는 같음처럼 작동하지 않습니다(즉, 둘 `dist(x,y)` `dist(y,z)` 다 와 true인 `dist(x,z)` 경우 는 true도 따르지 않습니다.) *내부적으로는 때때로 이를 "대각선 조인"이라고 부릅니다.*

예를 들어 비교적 작은 시간 기간 내에 이벤트 시퀀스를 식별하려고 한다고 가정합니다. 이 예제를 보여 주기 위해 `T` 다음 스키마가 있는 테이블이 있다고 가정합니다.

- `SessionId`: 상관 관계 `string` 아이디가 있는 형식의 열입니다.
- `EventType`: 레코드의 `string` 이벤트 유형을 식별하는 형식의 열입니다.
- `Timestamp`: 레코드에 `datetime` 의해 설명된 이벤트가 발생한 시기를 나타내는 형식열입니다.

```kusto
let T = datatable(SessionId:string, EventType:string, Timestamp:datetime)
[
    '0', 'A', datetime(2017-10-01 00:00:00),
    '0', 'B', datetime(2017-10-01 00:01:00),
    '1', 'B', datetime(2017-10-01 00:02:00),
    '1', 'A', datetime(2017-10-01 00:03:00),
    '3', 'A', datetime(2017-10-01 00:04:00),
    '3', 'B', datetime(2017-10-01 00:10:00),
];
T
```

|SessionId|EventType|타임스탬프|
|---|---|---|
|0|A|2017-10-01 00:00:00.0000000|
|0|b|2017-10-01 00:01:00.0000000|
|1|b|2017-10-01 00:02:00.0000000|
|1|A|2017-10-01 00:03:00.0000000|
|3|A|2017-10-01 00:04:00.0000000|
|3|b|2017-10-01 00:10:00.0000000|


**문제 설명**

쿼리가 다음 질문에 답하기를 원합니다.

   이벤트 유형 `A` 다음에 `B` `1min` 시간 내에 이벤트 유형이 뒤따르는 모든 세션 아이디를 찾습니다.

(위의 샘플 데이터에서 이러한 세션 ID만 `0`.)

비효율적이기는 하지만 다음 쿼리가 이 질문에 답변합니다.

```kusto
T 
| where EventType == 'A'
| project SessionId, Start=Timestamp
| join kind=inner
    (
    T 
    | where EventType == 'B'
    | project SessionId, End=Timestamp
    ) on SessionId
| where (End - Start) between (0min .. 1min)
| project SessionId, Start, End 

```

|SessionId|시작|끝|
|---|---|---|
|0|2017-10-01 00:00:00.0000000|2017-10-01 00:01:00.0000000|

이 쿼리를 최적화하기 위해 시간 창이 조인 키로 표현되도록 아래에 설명된 대로 다시 작성할 수 있습니다.

**시간 창을 고려하여 쿼리 다시 작성**

이 아이디어는 `datetime` 값이 시간 창크기의 절반인 버킷에 "불연속화"되도록 쿼리를 다시 작성하는 것입니다.
그런 다음 Kusto의 등가 를 사용하여 버킷 아이디를 비교할 수 있습니다.

```kusto
let lookupWindow = 1min;
let lookupBin = lookupWindow / 2.0; // lookup bin = equal to 1/2 of the lookup window
T 
| where EventType == 'A'
| project SessionId, Start=Timestamp,
          // TimeKey on the left side of the join is mapped to a discrete time axis for the join purpose
          TimeKey = bin(Timestamp, lookupBin)
| join kind=inner
    (
    T 
    | where EventType == 'B'
    | project SessionId, End=Timestamp,
              // TimeKey on the right side of the join - emulates event 'B' appearing several times
              // as if it was 'replicated'
              TimeKey = range(bin(Timestamp-lookupWindow, lookupBin),
                              bin(Timestamp, lookupBin),
                              lookupBin)
    // 'mv-expand' translates the TimeKey array range into a column
    | mv-expand TimeKey to typeof(datetime)
    ) on SessionId, TimeKey 
| where (End - Start) between (0min .. lookupWindow)
| project SessionId, Start, End 
```

|SessionId|시작|끝|
|---|---|---|
|0|2017-10-01 00:00:00.0000000|2017-10-01 00:01:00.0000000|


**실행 가능한 쿼리 참조(테이블이 인라인처리된 경우)**

```kusto
let T = datatable(SessionId:string, EventType:string, Timestamp:datetime)
[
    '0', 'A', datetime(2017-10-01 00:00:00),
    '0', 'B', datetime(2017-10-01 00:01:00),
    '1', 'B', datetime(2017-10-01 00:02:00),
    '1', 'A', datetime(2017-10-01 00:03:00),
    '3', 'A', datetime(2017-10-01 00:04:00),
    '3', 'B', datetime(2017-10-01 00:10:00),
];
let lookupWindow = 1min;
let lookupBin = lookupWindow / 2.0;
T 
| where EventType == 'A'
| project SessionId, Start=Timestamp, TimeKey = bin(Timestamp, lookupBin)
| join kind=inner
    (
    T 
    | where EventType == 'B'
    | project SessionId, End=Timestamp,
              TimeKey = range(bin(Timestamp-lookupWindow, lookupBin),
                              bin(Timestamp, lookupBin),
                              lookupBin)
    | mv-expand TimeKey to typeof(datetime)
    ) on SessionId, TimeKey 
| where (End - Start) between (0min .. lookupWindow)
| project SessionId, Start, End 
```

|SessionId|시작|끝|
|---|---|---|
|0|2017-10-01 00:00:00.0000000|2017-10-01 00:01:00.0000000|


**50M 데이터 쿼리**

다음 쿼리는 50M 레코드 및 ~10M ID의 데이터 집합을 에뮬레이트하고 위에서 설명한 기술로 쿼리를 실행합니다.

```kusto
let T = range x from 1 to 50000000 step 1
| extend SessionId = rand(10000000), EventType = rand(3), Time=datetime(2017-01-01)+(x * 10ms)
| extend EventType = case(EventType <= 1, "A",
                          EventType <= 2, "B",
                          "C");
let lookupWindow = 1min;
let lookupBin = lookupWindow / 2.0;
T 
| where EventType == 'A'
| project SessionId, Start=Time, TimeKey = bin(Time, lookupBin)
| join kind=inner
    (
    T 
    | where EventType == 'B'
    | project SessionId, End=Time, 
              TimeKey = range(bin(Time-lookupWindow, lookupBin), 
                              bin(Time, lookupBin),
                              lookupBin)
    | mv-expand TimeKey to typeof(datetime)
    ) on SessionId, TimeKey 
| where (End - Start) between (0min .. lookupWindow)
| project SessionId, Start, End 
| count 
```

|개수|
|---|
|1276|