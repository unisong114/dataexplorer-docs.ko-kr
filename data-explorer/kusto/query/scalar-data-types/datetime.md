---
title: datetime 데이터 형식 - Azure Data Explorer | Microsoft Docs
description: 이 문서에서는 Azure Data Explorer의 datetime 데이터 형식에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.localizationpriority: high
ms.openlocfilehash: e65b16ac4a280f2ecbef2c4557cac4a8d7be13b3
ms.sourcegitcommit: f49e581d9156e57459bc69c94838d886c166449e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2020
ms.locfileid: "95513134"
---
# <a name="the-datetime-data-type"></a>datetime 데이터 형식

`datetime`(`date`) 데이터 형식은 일반적으로 날짜와 시간으로 표현되는 한 순간을 나타냅니다.
값 범위는 서기(공통 시대) 0001년 1월 1일 00:00:00(자정)에서 서기 9999년 12월 31일 오후 11:59:59까지 입니다. (C.E.) 그레고리오력에서 

시간 값은 틱이라는 100나노초 단위로 측정되고 특정 날짜는 서기 0001년 1월 1일 자정 12:00 이후의 틱 수입니다. (C.E.) 그레고리오력에서(윤초에 의해 추가되는 틱 제외)
예를 들어 틱 값 31241376000000000은 날짜, 0100년 1월 1일 12:00:00 자정을 나타냅니다.
이를 "선형 시간의 순간"이라고도 합니다.

> [!WARNING]
> Kusto의 `datetime` 값은 항상 UTC 표준 시간대에 있습니다. 다른 표준 시간대에 `datetime` 값을 표시하는 것은 데이터 자체의 속성이 아니라 데이터를 표시하는 사용자 애플리케이션의 책임입니다. 데이터의 일부로 표준 시간대 값을 유지해야 경우 별도의 열을 사용해야 합니다(UTC와 관련된 오프셋 정보 제공).

## <a name="datetime-literals"></a>datetime 리터럴

`datetime` 형식의 리터럴에는 `datetime(`*값*`)` 구문이 있습니다. 여기서 다음 표에 표시된 것처럼 *값* 에 대해 여러 형식이 지원됩니다.

|예제                                                     |값                                                         |
|------------------------------------------------------------|--------------------------------------------------------------|
|`datetime(2015-12-31 23:59:59.9)`<br/>`datetime(2015-12-31)`|시간은 항상 UTC 기준입니다. 날짜를 생략하면 오늘 시간을 제공합니다.|
|`datetime(null)`                                            |[null 값](null-values.md)을 참조하세요.                            |
|`now()`                                                     |현재 시간입니다.                                             |
|`now(`-*timespan*`)`                                        |`now()-`*timespan*                                            |
|`ago(`*timespan*`)`                                         |`now()-`*timespan*                                            |

`now()` 및 `ago()`는 Kusto가 쿼리 실행을 시작한 시점과 비교하여 `datetime` 값을 표시합니다. 이러한 값은 동일한 쿼리에서 여러 번 나타날 수 있으며 모두에 대해 단일 값이 사용됩니다.
(즉, `now(-x) - ago(x)`와 같은 식은 항상 `timespan` 값 0으로 평가됩니다.)

## <a name="supported-formats"></a>지원되는 형식

[datetime() 리터럴](#datetime-literals) 및 [todatetime()](../todatetimefunction.md) 함수로 지원되는 `datetime`에 대한 몇 가지 형식이 있습니다.

> [!WARNING]
> ISO 8601 형식만 사용하는 것을 **적극 권장** 합니다.

### <a name="iso-8601"></a>[ISO 8601](https://www.iso.org/iso/home/standards/iso8601.htm)

|형식|예제|
|------|-------|
|%Y-%m-%dT%H:%M:%s%z|2014-05-25T08:20:03.123456Z|
|%Y-%m-%dT%H:%M:%s|2014-05-25T08:20:03.123456|
|%Y-%m-%dT%H:%M|2014-05-25T08:20|
|%Y-%m-%d %H:%M:%s%z|2014-11-08 15:55:55.123456Z|
|%Y-%m-%d %H:%M:%s|2014-11-08 15:55:55|
|%Y-%m-%d %H:%M|2014-11-08 15:55|
|%Y-%m-%d|2014-11-08|

### <a name="rfc-822"></a>[RFC 822](https://www.ietf.org/rfc/rfc0822.txt)

|형식|예제|
|------|-------|
|%w, %e %b %r %H:%M:%s %Z|14년 11월 8일 토요일, 15:05:02 GMT|
|%w, %e %b %r %H:%M:%s|14년 11월 8일 토요일, 15:05:02|
|%w, %e %b %r %H:%M|14년 11월 8일 토요일, 15:05|
|%w, %e %b %r %H:%M %Z|14년 11월 8일 토요일, 15:05 GMT|
|%e %b %r %H:%M:%s %Z|14년 11월 8일 15:05:02 GMT|
|%e %b %r %H:%M:%s|14년 11월 8일 15:05:02|
|%e %b %r %H:%M|14년 11월 8일 15:05|
|%e %b %r %H:%M %Z|14년 11월 8일 15:05 GMT|

### <a name="rfc-850"></a>[RFC 850](https://tools.ietf.org/html/rfc850)

|형식|예제|
|------|-------|
|%w, %e-%b-%r %H:%M:%s %Z|2014년 11월 8일 토요일 15:05:02 GMT|
|%w, %e-%b-%r %H:%M:%s|2014년 11월 8일 토요일 15:05:02|
|%w, %e-%b-%r %H:%M %Z|2014년 11월 8일 토요일 15:05 GMT|
|%w, %e-%b-%r %H:%M|2014년 11월 8일 토요일 15:05|
|%e-%b-%r %H:%M:%s %Z|2014년 11월 8일 15:05:02 GMT|
|%e-%b-%r %H:%M:%s|2014년 11월 8일 15:05:02|
|%e-%b-%r %H:%M %Z|2014년 11월 8일 15:05 GMT|
|%e-%b-%r %H:%M|2014년 11월 8일 15:05|


### <a name="sortable"></a>정렬 가능 

|형식|예제|
|------|-------|        
|%Y-%n-%e %H:%M:%s|2014-11-08 15:05:25|
|%Y-%n-%e %H:%M:%s %Z|2014-11-08 15:05:25 GMT|
|%Y-%n-%e %H:%M|2014-11-08 15:05|
|%Y-%n-%e %H:%M %Z|2014-11-08 15:05 GMT|
|%Y-%n-%eT%H:%M:%s|2014-11-08T15:05:25|
|%Y-%n-%eT%H:%M:%s %Z|2014-11-08T15:05:25 GMT|
|%Y-%n-%eT%H:%M|2014-11-08T15:05|
|%Y-%n-%eT%H:%M %Z|2014-11-08T15:05 GMT|
