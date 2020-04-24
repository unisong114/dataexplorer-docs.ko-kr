---
title: unixtime_microseconds_todatetime() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 unixtime_microseconds_todatetime()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 11/27/2019
ms.openlocfilehash: 6ab593049fcafeba27b8158a1c9c6a5b6fa75fe3
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81505273"
---
# <a name="unixtime_microseconds_todatetime"></a>unixtime_microseconds_todatetime()

유닉스 에포크 마이크로초를 UTC 날짜 시간으로 변환합니다.

**구문**

`unixtime_microseconds_todatetime(*microseconds*)`

**인수**

* *마이크로초*: 실제 숫자는 마이크로초단위로 에포크 타임스탬프를 나타냅니다. `Datetime`epoch 시간(1970-01-01 00:00) 이전에 발생하는 경우는 음수 타임스탬프 값이 있습니다.

**반환**

전환이 성공하면 결과는 [날짜 시간](./scalar-data-types/datetime.md) 값이 됩니다. 변환에 성공하지 못하면 결과는 null이 됩니다.

**참고 항목**

* [unixtime_seconds_todatetime()](unixtime-seconds-todatetimefunction.md)을 사용하여 유닉스 에포크 초를 UTC 날짜 시간으로 변환합니다.
* [unixtime_milliseconds_todatetime()를](unixtime-milliseconds-todatetimefunction.md)사용하여 유닉스 에포크 밀리초를 UTC 날짜 시간으로 변환합니다.
* [unixtime_nanoseconds_todatetime()](unixtime-nanoseconds-todatetimefunction.md)을 사용하여 유닉스 에이프릴 나노초를 UTC 날짜 시간으로 변환합니다.

**예제**

```kusto
print date_time = unixtime_microseconds_todatetime(1546300800000000)
```

|date_time|
|---|
|2019-01-01 00:00:00.0000000|