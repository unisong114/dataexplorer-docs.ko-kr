---
title: 날짜 시간 데이터 형식 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 날짜 시간 데이터 형식에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: a5a234c49a5bb5b04ccde49e7fb3702d927e38b5
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81509914"
---
# <a name="the-datetime-data-type"></a>날짜 시간 데이터 형식

`datetime` ()`date`데이터 형식은 일반적으로 날짜 와 시간으로 표현 되는 시간의 순간을 나타냅니다.
값은 00:00:00 (자정), 0001 년 1 월 1 일 안노 도미니 (공통 시대)에서 11:59:59 P.M., 9999 A.D. (C.E.) 그레고리오력에서. 

시간 값은 틱이라고 하는 100나노초 단위로 측정되며, 특정 날짜는 0001년 1월 1일 자정 12:00 이후의 틱 수입니다. (C.E.) [그레고리오캘린더 달력]에서 (윤초에 의해 추가될 틱은 제외).
예를 들어 31241376000000000의 틱 값은 0100 12:00:00 자정 날짜, 금요일, 1월 01일을 나타냅니다.
이를 "선형 시간의 순간"이라고도 합니다.

> [!WARNING]
> Kusto의 `datetime` 값은 항상 UTC 표준 시간대에 있습니다. 다른 `datetime` 표준 시간대에 값을 표시하는 것은 데이터 자체의 속성이 아니라 데이터를 표시하는 사용자 응용 프로그램의 책임입니다. 표준 시간대 값을 데이터의 일부로 유지해야 하는 경우 별도의 열을 사용해야 합니다(UTC를 기준으로 오프셋 정보 제공).

## <a name="datetime-literals"></a>datetime 리터럴

문자 `datetime` 문자에는 구문 `datetime(` *값이*`)`있으며 다음 표에 표시된 대로 값에 대해 여러 형식이 지원됩니다. *value*

|예제                                                     |값                                                         |
|------------------------------------------------------------|--------------------------------------------------------------|
|`datetime(2015-12-31 23:59:59.9)`<br/>`datetime(2015-12-31)`|시간은 항상 UTC 기준입니다. 날짜를 생략하면 오늘 시간을 제공합니다.|
|`datetime(null)`                                            |[null 값을](null-values.md)참조하십시오.                            |
|`now()`                                                     |현재 시간입니다.                                             |
|`now(`-*Timespan*`)`                                        |`now()-`*Timespan*                                            |
|`ago(`*Timespan*`)`                                         |`now()-`*Timespan*                                            |

`now()`Kusto가 쿼리를 `ago()` `datetime` 실행하기 시작한 순간과 비교하여 값을 나타냅니다. 동일한 쿼리에서 여러 번 나타날 수 있으며 단일 값이 모두 사용됩니다.
즉, 항상 0의 값으로 평가되는 식과 `now(-x) - ago(x)` 같은 식입니다. `timespan`

## <a name="supported-formats"></a>지원되는 형식

`datetime` [datetime() 리터럴](#datetime-literals) 및 [todatetime()](../todatetimefunction.md) 함수로 지원되는 몇 가지 형식이 있습니다.

> [!WARNING]
> ISO 8601 형식만 사용하는 **것이 좋습니다.**

### <a name="iso-8601"></a>[ISO 8601](https://www.iso.org/iso/home/standards/iso8601.htm)

|형식|예제|
|------|-------|
|%Y%m%dT%H%:M%s%z|2014-05-25T08:20:03.123456Z|
|%Y%m%dT%H:%M:% "|2014-05-25T08:20:03.123456|
|%Y%m%dT%H%M"|2014-05-25T08:20|
|%Y%m%d%H:%M:%s%z"|2014-11-08 15:55:55.123456Z|
|%Y%m%d%H:%M:% "|2014-11-08 15:55:55|
|%Y%m%d%H:M"|2014-11-08 15:55|
|%Y%m%d|2014-11-08|

### <a name="rfc-822"></a>[RFC 822](https://www.ietf.org/rfc/rfc0822.txt)

|형식|예제|
|------|-------|
|%w, %e %b %r %H%:%M%Z|11월 8일(일) 11월 14일 15:05:02 GMT|
|%w, %e %b %r %H%:M%s|11월 8일(일) 11월 14일 15:05:02|
|%w, %e %b %r %H%M|11월 8일(일) 11월 14일(일) 15:05|
|%w, %e %b %r %H%M%Z|11월 8일(일) 11월 14일 15:05 GMT|
|%e %b%r%H%:%M%S%Z|8 11월 14일 15:05:02 GMT|
|%e %b%r%H%:%M%s"|8 11월 14일 15:05:02|
|%e %b %r %H%M"|8 11월 14일 15:05|
|%e %b%r%H%M%Z"|8 11월 14일 15:05 GMT|

### <a name="rfc-850"></a>[RFC 850](https://tools.ietf.org/html/rfc850)

|형식|예제|
|------|-------|
|%w, %e%b-%r%H:%M%S%Z|토요일, 08-11 월-14 15:05:02 GMT|
|%w, %e%b-%r%H:%M:%s|토요일 08-11월 14일 15:05:02|
|%w, %e%b-%r%H:%M%Z|토요일, 08-11 월-14 15:05 GMT|
|%w, %e%b-%r%H:%M|토요일 08-11월 14일 15:05|
|%e%b-%r%H%:M%%S%Z|08-11월-14 15:05:02 GMT|
|%e%b-%r%H:%M%s"|11월 08일~14일 15:05:02|
|%e%b-%r%H%M%Z"|08-11월-14 15:05 GMT|
|%e%b-%r%H%M"|11월 08일~14일 15:05|


### <a name="sortable"></a>정렬 가능 

|형식|예제|
|------|-------|        
|%Y%n%e%H%:M%s|2014-11-08 15:05:25|
|%Y%n%e%H%:%M%%Z|2014-11-08 15:05:25 GMT|
|%Y%n%e%H:M|2014-11-08 15:05|
|%Y%n%e%H%:M%Z|2014-11-08 15:05 GMT|
|%Y%n%eT%H%:M%s|2014-11-08T15:05:25|
|%Y%n%eT%H%:%M%S%Z|2014-11-08T15:05:25 GMT|
|%Y%n%eT%H%M|2014-11-08T15:05|
|%Y%n%eT%H%:M%Z"|2014-11-08T15:05 GMT|