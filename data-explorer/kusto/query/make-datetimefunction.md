---
title: make_datetime ()-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기에서 make_datetime ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 589be4fbbc285309e86647ce8d7392840aedea0e
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347003"
---
# <a name="make_datetime"></a>make_datetime()

지정 된 날짜 및 시간에서 [datetime](./scalar-data-types/datetime.md) 스칼라 값을 만듭니다.

```kusto
make_datetime(2017,10,01,12,10) == datetime(2017-10-01 12:10)
```

## <a name="syntax"></a>Syntax

`make_datetime(`*년*,*월*,*일*`)`

`make_datetime(`*년*,*월*,*일*,*시*,*분*`)`

`make_datetime(`*년*,*월*,*일*,*시*,*분*,*초*`)`

## <a name="arguments"></a>인수

* *year*: year (0에서 9999 사이의 정수 값)
* *month*: month (1에서 12 사이의 정수 값)
* *day*: day (1에서 28-31 사이의 정수 값)
* *시간*: 시간 (0에서 23 사이의 정수 값)
* *분*: 분 (0에서 59 사이의 정수 값)
* *second*: second (0에서 59.9999999 사이의 실제 값)

## <a name="returns"></a>반환

성공적으로 생성 되 면 결과는 [datetime](./scalar-data-types/datetime.md) 값이 되며, 그렇지 않으면 결과는 null이 됩니다.
 
## <a name="example"></a>예제

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

