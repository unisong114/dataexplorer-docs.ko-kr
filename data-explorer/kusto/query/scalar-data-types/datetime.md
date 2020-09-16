---
title: Datetime 데이터 형식-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 datetime 데이터 형식에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: a1c4b89677e08872fdf81e72f7391511b7d6c637
ms.sourcegitcommit: 313a91d2a34383b5a6e39add6c8b7fabb4f8d39a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/16/2020
ms.locfileid: "90680709"
---
# <a name="the-datetime-data-type"></a>Datetime 데이터 형식

`datetime`( `date` ) 데이터 형식은 일반적으로 날짜와 시간으로 표시 된 시간을 나타냅니다.
값의 범위는 00:00:00 (자정), 01 년 1 월 1 일 Anno 기 (공통 연대)부터 11:59:59 년 12 월 31 일, 9999 A.D. 까지입니다. 그레고리오력에서 서 기 양력. 

시간 값은 틱 이라는 100 나노초 단위로 측정 되 고 특정 날짜는 0001 년 1 월 1 일 자정 12:00 이후의 틱 수입니다. 그레고리오력에서 서 기 GregorianCalendar 달력에서 (윤 초에 의해 추가 되는 틱 제외)
예를 들어 틱 값 31241376000000000은 날짜, 금요일, 12:00:00 0100 01 년 1 월 1 일 자정을 나타냅니다.
이를 "선형 시간에서 잠시" 이라고 합니다.

> [!WARNING]
> `datetime`Kusto의 값은 항상 UTC 표준 시간대에 있습니다. `datetime`다른 표준 시간대에 값을 표시 하는 것은 데이터 자체의 속성이 아니라 데이터를 표시 하는 사용자 응용 프로그램의 책임입니다. 표준 시간대 값이 데이터의 일부로 유지 되어야 하는 경우 별도의 열을 사용 해야 합니다 (UTC에 상대적인 오프셋 정보 제공).

## <a name="datetime-literals"></a>datetime 리터럴

형식의 리터럴에는 `datetime` `datetime(` *value* `)` 다음 표에 표시 된 것과 같이 *값*에 대해 많은 형식이 지원 되는 구문 값이 있습니다.

|예제                                                     |값                                                         |
|------------------------------------------------------------|--------------------------------------------------------------|
|`datetime(2015-12-31 23:59:59.9)`<br/>`datetime(2015-12-31)`|시간은 항상 UTC 기준입니다. 날짜를 생략하면 오늘 시간을 제공합니다.|
|`datetime(null)`                                            |[Null 값](null-values.md)을 참조 하십시오.                            |
|`now()`                                                     |현재 시간입니다.                                             |
|`now(`-*timespan*`)`                                        |`now()-`*timespan*                                            |
|`ago(`*timespan*`)`                                         |`now()-`*timespan*                                            |

`now()` 및 `ago()` 는 `datetime` kusto가 쿼리 실행을 시작 하는 순간과 비교할 때의 값을 표시 합니다. 이러한 값은 동일한 쿼리에서 여러 번 나타날 수 있으며 모두에 대해 단일 값이 사용 됩니다.
즉,와 같은 식이 `now(-x) - ago(x)` 항상 `timespan` 0 값으로 평가 됩니다.

## <a name="supported-formats"></a>지원되는 형식

에는 `datetime` [datetime () 리터럴과](#datetime-literals) [system.xml.xmlconvert.todatetime ()](../todatetimefunction.md) 함수로 지원 되는 여러 가지 형식이 있습니다.

> [!WARNING]
> ISO 8601 형식만 사용 하는 것이 **좋습니다** .

### <a name="iso-8601"></a>[ISO 8601](https://www.iso.org/iso/home/standards/iso8601.htm)

|형식|예제|
|------|-------|
|% Y-% m-% dT% H:%M:% s% z|2014-05-25T08:20:03.123456 Z|
|% Y-% m-% dT% H:%M:% s|2014-05-25T08:20:03.123456|
|% Y-% m-% dT% H:%M|2014-05-25T08:20|
|% Y-% m-% d% H:%M:% s% z|2014-11-08 15:55:55.123456 z|
|% Y-% m-% d% H:%M:% s|2014-11-08 15:55:55|
|% Y-% m-% d% H:%M|2014-11-08 15:55|
|% Y-% m-% d|2014-11-08|

### <a name="rfc-822"></a>[RFC 822](https://www.ietf.org/rfc/rfc0822.txt)

|형식|예제|
|------|-------|
|% w,% e% b% r% H:%M:% s% Z|토요일, 14 15:05:02 년 11 월 8 일 GMT|
|% w,% e% b% r% H:%M:% s|토요일, 14 15:05:02 년 11 월 8 일|
|% w,% e% b% r% H:%M|토요일, 14 15:05 년 11 월 8 일|
|% w,% e% b% r% H:%M% Z|토요일, 14 15:05 년 11 월 8 일 GMT|
|% e% b% r% H:%M:% s% Z|14 15:05:02 년 11 월 8 일 GMT|
|% e% b% r% H:%M:% s|14 15:05:02 년 11 월 8 일|
|% e% b% r% H:%M|14 15:05 년 11 월 8 일|
|% e% b% r% H:%M% Z|14 15:05 년 11 월 8 일 GMT|

### <a name="rfc-850"></a>[RFC 850](https://tools.ietf.org/html/rfc850)

|형식|예제|
|------|-------|
|% w,% e-% b-% r% H:%M:% s% Z|토요일, 08-11 월-14 15:05:02 GMT|
|% w,% e-% b-% r% H:%M:% s|토요일, 08-11 월-14 15:05:02|
|% w,% e-% b-% r% H:%M% Z|토요일, 08-11 월-14 15:05 GMT|
|% w,% e-% b-% r% H:%M|토요일, 08-11 월-14 15:05|
|% e-% b-% r% H:%M:% s% Z|08 년 11 월-14 15:05:02 GMT|
|% e-% b-% r% H:%M:% s|08-11 월-14 15:05:02|
|% e-% b-% r% H:%M% Z|08 년 11 월-14 15:05 GMT|
|% e-% b-% r% H:%M|08-11 월-14 15:05|


### <a name="sortable"></a>8601 

|형식|예제|
|------|-------|        
|% Y-% n-% e% H:%M:% s|2014-11-08 15:05:25|
|% Y-% n-% e% H:%M:% s% Z|2014-11-08 15:05:25 GMT|
|% Y-% n-% e% H:%M|2014-11-08 15:05|
|% Y-% n-% e% H:%M% Z|2014-11-08 15:05 GMT|
|% Y-% n-% eT% H:%M:% s|2014-11-08T15:05:25|
|% Y-% n-% eT% H:%M:% s% Z|2014-11-08T15:05:25 GMT|
|% Y-% n-% eT% H:%M|2014-11-08T15:05|
|% Y-% n-% eT% H:%M% Z|2014-11-08T15:05 GMT|
