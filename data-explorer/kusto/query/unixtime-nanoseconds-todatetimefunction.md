---
title: unixtime_nanoseconds_todatetime() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 unixtime_nanoseconds_todatetime()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 11/25/2019
ms.openlocfilehash: cd9dadca7ab4455f8a90d5a7842293b2d6c8bcc2
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81505239"
---
# <a name="unixtime_nanoseconds_todatetime"></a>unixtime_nanoseconds_todatetime()

유닉스 에보 나노초를 UTC 날짜 시간으로 변환합니다.

**구문**

`unixtime_nanoseconds_todatetime(*nanoseconds*)`

**인수**

* *나노초*: 실제 숫자는 나노초단위로 에포크 타임스탬프를 나타냅니다. `Datetime`epoch 시간(1970-01-01 00:00) 이전에 발생하는 경우는 음수 타임스탬프 값이 있습니다.

**반환**

전환이 성공하면 결과는 [날짜 시간](./scalar-data-types/datetime.md) 값이 됩니다. 변환에 성공하지 못하면 결과는 null이 됩니다.

**참고 항목**

* [unixtime_seconds_todatetime()](unixtime-seconds-todatetimefunction.md)을 사용하여 유닉스 에포크 초를 UTC 날짜 시간으로 변환합니다.
* [unixtime_milliseconds_todatetime()를](unixtime-milliseconds-todatetimefunction.md)사용하여 유닉스 에포크 밀리초를 UTC 날짜 시간으로 변환합니다.
* [unixtime_microseconds_todatetime()](unixtime-microseconds-todatetimefunction.md)을 사용하여 유닉스 에포크 마이크로초를 UTC 날짜 시간으로 변환합니다.

**예제**

```kusto
print date_time = unixtime_nanoseconds_todatetime(1546300800000000000)
```

|date_time|
|---|
|2019-01-01 00:00:00.0000000|