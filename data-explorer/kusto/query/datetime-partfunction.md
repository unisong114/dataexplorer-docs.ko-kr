---
title: datetime_part() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 datetime_part()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/18/2020
ms.openlocfilehash: c64208725f0d5c49a7ea7733f8eb5a208e19225b
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81516374"
---
# <a name="datetime_part"></a>datetime_part()

요청된 날짜 파트를 정수 값으로 추출합니다.

```kusto
datetime_part("Day",datetime(2015-12-14))
```

**구문**

`datetime_part(`*부품*`,`*날짜 시간*`)`

**인수**

* `date`: `datetime`
* `part`: `string`

가능한 `part`값 : 
- Year
- Quarter
- 월
- week_of_year
- 일
- DayOfYear
- Hour
- Minute
- 초
- Millisecond
- 마이크로초
- 나노초

**반환**

추출된 부분을 나타내는 정수입니다.

**참고**

`week_of_year`는 주 번호를 나타내는 정수를 반환합니다. 주 번호는 첫 번째 목요일을 포함하는 연도의 첫 번째 주부터 계산됩니다.

**예**

```kusto
let dt = datetime(2017-10-30 01:02:03.7654321); 
print 
year = datetime_part("year", dt),
quarter = datetime_part("quarter", dt),
month = datetime_part("month", dt),
weekOfYear = datetime_part("week_of_year", dt),
day = datetime_part("day", dt),
dayOfYear = datetime_part("dayOfYear", dt),
hour = datetime_part("hour", dt),
minute = datetime_part("minute", dt),
second = datetime_part("second", dt),
millisecond = datetime_part("millisecond", dt),
microsecond = datetime_part("microsecond", dt),
nanosecond = datetime_part("nanosecond", dt)

```

|year|quarter|month|weekOfYear|일|dayOfYear|hour|minute|second|밀리초|microsecond|나노초|
|---|---|---|---|---|---|---|---|---|---|---|---|
|2017|4|10|44|30|303|1|2|3|765|765432|765432100|

> [!NOTE]
> `weekofyear`는 파트의 사용되지 `week_of_year` 않는 변형입니다. `weekofyear`ISO 8601을 준수하지 않았습니다. 연도의 첫 번째 주는 연도의 첫 번째 수요일이 있는 주로 정의되었습니다.
`week_of_year`ISO 8601을 준수합니다. 연도의 첫 번째 주는 연도의 첫 번째 목요일이 있는 주로 정의됩니다. [자세한 내용은](https://en.wikipedia.org/wiki/ISO_8601#Week_dates).