---
title: datetime_add() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기에서 datetime_add()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: ead7e0ae5c4dee94930afe1b20c4d5b99e2b4664
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81516459"
---
# <a name="datetime_add"></a>datetime_add()

지정된 [날짜에서](./scalar-data-types/datetime.md) 새 날짜 시간을 계산합니다part 지정된 날짜에 추가 된 [datetime](./scalar-data-types/datetime.md)금액을 곱합니다.

**구문**

`datetime_add(`*기간*`,`*금액*`,`*날짜 시간*`)`

**인수**

* `period`: [문자열](./scalar-data-types/string.md). 
* `amount`: [정수](./scalar-data-types/int.md).
* `datetime`: [날짜 시간](./scalar-data-types/datetime.md) 값입니다.

*기간의*가능한 값 : 
- Year
- Quarter
- 월
- Week
- 일
- Hour
- Minute
- 초
- Millisecond
- 마이크로초
- 나노초

**반환**

특정 시간/날짜 간격 이후의 날짜가 추가되었습니다.

**예**

```kusto
print  year = datetime_add('year',1,make_datetime(2017,1,1)),
quarter = datetime_add('quarter',1,make_datetime(2017,1,1)),
month = datetime_add('month',1,make_datetime(2017,1,1)),
week = datetime_add('week',1,make_datetime(2017,1,1)),
day = datetime_add('day',1,make_datetime(2017,1,1)),
hour = datetime_add('hour',1,make_datetime(2017,1,1)),
minute = datetime_add('minute',1,make_datetime(2017,1,1)),
second = datetime_add('second',1,make_datetime(2017,1,1))

```

|year|quarter|month|week|일|hour|minute|second|
|---|---|---|---|---|---|---|---|
|2018-01-01 00:00:00.0000000|2017-04-01 00:00:00.0000000|2017-02-01 00:00:00.0000000|2017-01-08 00:00:00.0000000|2017-01-02 00:00:00.0000000|2017-01-01 01:00:00.0000000|2017-01-01 00:01:00.0000000|2017-01-01 00:00:01.0000000|






