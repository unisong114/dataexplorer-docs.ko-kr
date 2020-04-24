---
title: make_datetime() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 make_datetime()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: c51b99e4d668ec4a5f96cdfd72442d7bcab553a5
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81512923"
---
# <a name="make_datetime"></a>make_datetime()

지정된 날짜 및 시간에서 [날짜 시간](./scalar-data-types/datetime.md) 스칼라 값을 만듭니다.

```kusto
make_datetime(2017,10,01,12,10) == datetime(2017-10-01 12:10)
```

**구문**

`make_datetime(`*연도,**월,**일*`)`

`make_datetime(`*연도,**월,**일,**시간,**분*`)`

`make_datetime(`*연도,**월,**일,**시간,**분,**두 번째*`)`

**인수**

* *연도*: 연도(정수 값, 0에서 9999까지)
* *월*: 월(정수 값, 1에서 12까지)
* *일*: 일 (정수 값, 1에서 28-31)
* *시간*: 시간(정수 값, 0에서 23까지)
* *분*: 분(정수 값, 0에서 59까지)
* *두 번째*: 두 번째 (실제 값, 0에서 59.99999999)

**반환**

생성에 성공하면 결과는 [datetime](./scalar-data-types/datetime.md) 값이 되고 그렇지 않으면 결과가 null이 됩니다.
 
**예제**

```kusto
print year_month_day = make_datetime(2017,10,01)
```

|year_month_day|
|---|
|2017-10-01 00:00:00.0000000|




```kusto
print year_month_day_hour_minute = make_datetime(2017,10,01,12,10)
```

|year_month_day_hour_minute|
|---|
|2017-10-01 12:10:00.0000000|




```kusto
print year_month_day_hour_minute_second = make_datetime(2017,10,01,12,11,0.1234567)
```

|year_month_day_hour_minute_second|
|---|
|2017-10-01 12:11:00.1234567|

