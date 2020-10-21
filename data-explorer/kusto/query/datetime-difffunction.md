---
title: datetime_diff ()-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기에서 datetime_diff ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 50ed62b60436fc13d679b5e729a84bcdcefa7275
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92247713"
---
# <a name="datetime_diff"></a>datetime_diff()

두 [날짜/시간](./scalar-data-types/datetime.md) 값의 calendarian 차이를 계산 합니다.

## <a name="syntax"></a>구문

`datetime_diff(`*기간* `,` *datetime_1* `,` *datetime_2*`)`

## <a name="arguments"></a>인수

* `period`: `string`. 
* `datetime_1`: [datetime](./scalar-data-types/datetime.md) 값입니다.
* `datetime_2`: [datetime](./scalar-data-types/datetime.md) 값입니다.

가능한 *기간*값: 
- Year
- Quarter
- 월
- 주
- 일
- Hour
- 분
- Second
- Millisecond
- 마이크로초
- 나노초

## <a name="returns"></a>반환

`periods`빼기 () 결과의 양을 나타내는 정수입니다 `datetime_1`  -  `datetime_2` .

## <a name="examples"></a>예

```kusto
print
year = datetime_diff('year',datetime(2017-01-01),datetime(2000-12-31)),
quarter = datetime_diff('quarter',datetime(2017-07-01),datetime(2017-03-30)),
month = datetime_diff('month',datetime(2017-01-01),datetime(2015-12-30)),
week = datetime_diff('week',datetime(2017-10-29 00:00),datetime(2017-09-30 23:59)),
day = datetime_diff('day',datetime(2017-10-29 00:00),datetime(2017-09-30 23:59)),
hour = datetime_diff('hour',datetime(2017-10-31 01:00),datetime(2017-10-30 23:59)),
minute = datetime_diff('minute',datetime(2017-10-30 23:05:01),datetime(2017-10-30 23:00:59)),
second = datetime_diff('second',datetime(2017-10-30 23:00:10.100),datetime(2017-10-30 23:00:00.900)),
millisecond = datetime_diff('millisecond',datetime(2017-10-30 23:00:00.200100),datetime(2017-10-30 23:00:00.100900)),
microsecond = datetime_diff('microsecond',datetime(2017-10-30 23:00:00.1009001),datetime(2017-10-30 23:00:00.1008009)),
nanosecond = datetime_diff('nanosecond',datetime(2017-10-30 23:00:00.0000000),datetime(2017-10-30 23:00:00.0000007))
```

|연도|quarter|month|week|일|hour|minute|second|밀리초|microsecond|나노초|
|---|---|---|---|---|---|---|---|---|---|---|
|17|2|13|5|29|2|5|10|100|100|-700|



