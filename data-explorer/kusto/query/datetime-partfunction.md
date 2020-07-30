---
title: datetime_part ()-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기에서 datetime_part ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/18/2020
ms.openlocfilehash: 2c1a73d2d7e31eb180b37fae3d392fd5792cd69b
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348516"
---
# <a name="datetime_part"></a>datetime_part()

요청 된 날짜 부분을 정수 값으로 추출 합니다.

```kusto
datetime_part("Day",datetime(2015-12-14))
```

## <a name="syntax"></a>Syntax

`datetime_part(`*파트* `,` *datetime*`)`

## <a name="arguments"></a>인수

* `date`: `datetime`
* `part`: `string`

의 가능한 값은 `part` 다음과 같습니다. 
- Year
- Quarter
- Month
- week_of_year
- 일
- DayOfYear
- Hour
- 분
- Second
- Millisecond
- 마이크로초
- 나노초

## <a name="returns"></a>반환

추출 된 파트를 나타내는 정수입니다.

**참고**

`week_of_year`주 번호를 나타내는 정수를 반환 합니다. 주 번호는 첫 번째 목요일을 포함 하는 1 년의 첫 번째 주에서 계산 됩니다.

## <a name="examples"></a>예제

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
> `weekofyear`은 (는) 사용 되지 않는 파트의 변형입니다 `week_of_year` . `weekofyear`ISO 8601 규격이 아닙니다. 1 년의 첫 번째 주는 연도의 첫 번째 주 수요일의 주로 정의 되었습니다.
`week_of_year`ISO 8601 호환 됨 1 년의 첫 번째 주는 해당 연도의 첫 번째 주 목요일의 주로 정의 됩니다. [자세한 내용](https://en.wikipedia.org/wiki/ISO_8601#Week_dates)