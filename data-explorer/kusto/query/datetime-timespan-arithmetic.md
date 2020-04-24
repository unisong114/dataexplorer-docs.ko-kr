---
title: 일시 적 / 시간 범위 산술 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 Datetime/timespan 산술 연산에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 11/27/2019
ms.openlocfilehash: 34bda7b8418dd519995f25c625a5031202a64a8b
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81516323"
---
# <a name="datetime--timespan-arithmetic"></a>일시 적 / 시간 범위 산술

Kusto는 형식 `datetime` 의 값에 대한 산술 연산 을 수행 지원합니다. `timespan`

* 두 `datetime` 값을 빼서 두 값을 빼서 `timespan` 차이를 표현하는 값을 얻을 수 있습니다.
  예를 들어, `datetime(1997-06-25) - datetime(1910-06-11)` 자크 [이브 쿠스토가](https://en.wikipedia.org/wiki/Jacques_Cousteau) 죽었을 때 의 나이는 몇 살입니까?

* 두 `timespan` 값을 추가하거나 빼서 합계 `timespan` 또는 차이인 값을 가져올 수 있습니다.
  예를 들어, `1d + 2d` 3일입니다.

* `datetime` 값에서 `timespan` 값을 추가하거나 뺄 수 있습니다.
  예를 들어, `datetime(1910-06-11) + 1d` 쿠스토가 하루 전에 돌아온 날짜입니다.

* 하나는 두 `timespan` 값을 나누어 몫을 얻을 수 있습니다.
  예를 `1d / 5h` `4.8`들어.
  이렇게 하면 모든 값을 `timespan` 다른 `timespan` 값의 배수로 표현할 수 있습니다. 예를 들어, 초 단위로 시간을 표현하려면 `1s` `1h / 1s` : (명백한 결과와 `3600`함께)로 나눕니다. `1h`

* 반대로 값을 얻기 위해 숫자 값(예: `double` `long`및)을 `timespan` 여러 개 `timespan` 씩 할 수 있습니다.
  예를 들어, 한 시간 반을 `1.5 * 1h`로 표현할 수 있습니다.

## <a name="example-unix-time"></a>예: 유닉스 시간

[유닉스](https://en.wikipedia.org/wiki/Unix_time) 시간(POSIX 시간 또는 UNIX Epoch time이라고도 함)은 1970년 1월 1일 목요일 00:00:00 이후 경과된 초수, 조정된 유니버설 타임(UTC)에서 윤초를 뺀 값으로 시점을 설명하는 시스템입니다.

데이터에 유닉스 시간을 정수로 표시하거나 정수로 변환해야 하는 경우 다음 기능을 사용할 수 있습니다.

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
> 위의 기능 외에도 유닉스 시대 시간 변환에 대한 전용 함수를 참조하십시오: [unixtime_seconds_todatetime()](unixtime-seconds-todatetimefunction.md)
> [unixtime_milliseconds_todatetime()](unixtime-milliseconds-todatetimefunction.md)
> [unixtime_microseconds_todatetime()](unixtime-microseconds-todatetimefunction.md)
> [unixtime_nanoseconds_todatetime()](unixtime-nanoseconds-todatetimefunction.md)