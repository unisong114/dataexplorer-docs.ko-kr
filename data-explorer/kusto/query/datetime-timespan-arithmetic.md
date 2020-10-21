---
title: Datetime/timespan 산술-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 Datetime/timespan 산술 연산을 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 11/27/2019
ms.openlocfilehash: 0b70cb1730d893e07790ade3079be21da209648c
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92247679"
---
# <a name="datetime--timespan-arithmetic"></a>Datetime/timespan 산술 연산

Kusto는 및 형식의 값에 대 한 산술 연산 수행을 지원 합니다 `datetime` `timespan` .

* 두 값을 빼서 두 `datetime` 값의 차이를 나타내는 값을 가져올 수 있습니다 `timespan` .
  예를 들어, `datetime(1997-06-25) - datetime(1910-06-11)` 이전 [Jacques Yves Cousteau](https://en.wikipedia.org/wiki/Jacques_Cousteau) .

* 두 값을 더하거나 빼서 `timespan` `timespan` 합계 또는 차이 값을 얻을 수 있습니다.
  예를 들어 `1d + 2d` 은 3 일입니다.

* 값에서 값을 더하거나 뺄 수 있습니다 `timespan` `datetime` .
  예를 들어 `datetime(1910-06-11) + 1d` 날짜 Cousteau는 1 일 이전으로 설정 된 날짜입니다.

* 두 `timespan` 값을 분할 하 여 몫을 가져올 수 있습니다.
  예를 들어는를 `1d / 5h` 제공 `4.8` 합니다.
  이를 통해 `timespan` 다른 값의 배수로 값을 표현할 수 `timespan` 있습니다. 예를 들어 1 시간 (초)을 표현 하려면 단순히 `1h` `1s` `1h / 1s` (명확한 결과)를 사용 하 여로 나눕니다. `3600`

* 반대로 값으로 숫자 값 (예: 및)을 여러 번 사용할 수 있습니다 `double` `long` `timespan` `timespan` .
  예를 들어 1 시간 및 절반을 표현할 수 있습니다 `1.5 * 1h` .

## <a name="example-unix-time"></a>예: Unix 시간

[Unix 시간](https://en.wikipedia.org/wiki/Unix_time) (POSIX 시간 또는 Unix Epoch 시간이 라고도 함)은 지정 시간을 00:00:00 목요일 이후 경과 된 시간 (초)으로 설명 하는 시스템으로, 1 월 1970 일, Utc (협정 세계시)를 뺀 시간을 뺀 값입니다.

데이터에 Unix 시간 표현이 정수로 포함 되어 있거나 변환 해야 하는 경우 다음 함수를 사용할 수 있습니다.

```kusto
let fromUnixTime = (t:long)
{ 
    datetime(1970-01-01) + t * 1sec 
};
print result = fromUnixTime(1546897531)
```

|result                     |
|---------------------------|
|2019-01-07 21:45:31.0000000|

```kusto
let toUnixTime = (dt:datetime) 
{ 
    (dt - datetime(1970-01-01)) / 1s 
};
print result = toUnixTime(datetime(2019-01-07 21:45:31.0000000))
```

|result                     |
|---------------------------|
|1546897531                 |

> [!NOTE]
> 위의 함수 외에도, unix epoch 시간 변환 전용 함수: [unixtime_seconds_todatetime ()](unixtime-seconds-todatetimefunction.md) 
>  [unixtime_milliseconds_todatetime (](unixtime-milliseconds-todatetimefunction.md)) 
>  [unixtime_microseconds_todatetime (](unixtime-microseconds-todatetimefunction.md)) 
>  [unixtime_nanoseconds_todatetime ()](unixtime-nanoseconds-todatetimefunction.md) 를 참조 하세요.