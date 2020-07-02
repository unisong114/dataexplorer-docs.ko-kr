---
title: 기간 내에 조인-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 기간 내에서 조인 하는 방법을 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 1ca7f38fa377be40cd290b04af65cc6fd59075cd
ms.sourcegitcommit: e093e4fdc7dafff6997ee5541e79fa9db446ecaa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/01/2020
ms.locfileid: "85763712"
---
# <a name="time-window-join"></a>시간 범위 조인

일부 높은 카디널리티 키에서 두 개의 큰 데이터 집합 간에 조인 하는 것이 유용한 경우가 많습니다. 작업 ID 또는 세션 ID와 같이, 왼쪽 및 오른쪽에 있는 datetime 열 사이의 "시간 거리"에 제한을 추가 하 여 각각의 왼쪽 ($left) 레코드와 일치 해야 하는 오른쪽 $right 레코드를 추가로 제한 합니다.

함수는 다음 시나리오에서와 같이 조인에 유용 합니다.
* 작업 ID 또는 세션 ID와 같은 고급 카디널리티 키에 따라 두 개의 큰 데이터 집합을 조인 합니다.
* 왼쪽 및 오른쪽에 있는 datetime 열 사이의 "시간 간격"에 제한을 추가 하 여 각 왼쪽 ($left) 레코드와 일치 해야 하는 오른쪽 $right 레코드를 제한 합니다.

*`equi-join`* 왼쪽 및 오른쪽 데이터 집합 간의 높은 카디널리티 키와 일치 하는 부분이 있으므로 시스템은 거리 함수를 적용 하 고이를 사용 하 여 조인을 크게 향상 시킬 수 있기 때문에 위의 작업은 일반적인 Kusto join 작업과 다릅니다.

> [!NOTE]
> 거리 함수는 동일 하 게 동작 하지 않습니다. 즉, 두 가지 방법 (x, y)과 배포 (y, z)가 모두 true 인 경우에는 해당 (x, z)도 마찬가지입니다. 내부적으로는이를 "대각선 조인"으로 지칭 하기도 합니다.

예를 들어 상대적으로 작은 기간 내에 이벤트 시퀀스를 식별 하려는 경우 다음 스키마를 포함 하는 테이블이 있다고 가정 합니다 `T` .

* `SessionId`: `string` 상관 관계 id가 포함 된 형식의 열입니다.
* `EventType`: `string` 레코드의 이벤트 유형을 식별 하는 형식의 열입니다.
* `Timestamp`: 형식의 열은 `datetime` 레코드에서 설명 하는 이벤트가 발생 한 시간을 나타냅니다.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
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

쿼리는 다음 질문에 대답 해야 합니다.

   이벤트 유형 뒤에 이벤트 유형이 뒤에 오는 모든 세션 Id를 찾습니다 `A` `B` `1min` .

> [!NOTE]
> 위의 샘플 데이터에서 유일 하 게 이러한 세션 ID는 `0` 입니다.

의미상이 질문에 대 한 답변은 다음 쿼리에서는 비효율적입니다.

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

이 쿼리를 최적화 하기 위해 시간 창이 조인 키로 표현 되도록 아래 설명 된 대로 다시 작성할 수 있습니다.

**시간 창을 고려 하 여 쿼리를 다시 작성 합니다.**

`datetime`값이 시간 창의 절반 크기에 해당 하는 버킷으로 "불연속화" 되도록 쿼리를 다시 작성 합니다. Kusto를 사용 *`equi-join`* 하 여 이러한 버킷 id를 비교 합니다.

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

**실행 가능한 쿼리 참조 (인라인 테이블 포함)**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
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

다음 쿼리는 50M 레코드 및 ~ 10M Id의 데이터 집합을 에뮬레이트하 며 위에 설명 된 기술을 사용 하 여 쿼리를 실행 합니다.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
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
