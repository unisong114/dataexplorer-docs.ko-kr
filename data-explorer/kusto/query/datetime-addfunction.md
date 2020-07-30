---
title: datetime_add ()-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기에서 datetime_add ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 766f0617b70e21194d731ae1cf8eabf1014265bb
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348550"
---
# <a name="datetime_add"></a>datetime_add()

지정 된 날짜/시간에 지정한 값을 곱하여 지정 된 [날짜/](./scalar-data-types/datetime.md)시간에 추가 된 새 [날짜/](./scalar-data-types/datetime.md) 시간을 계산 합니다.

## <a name="syntax"></a>Syntax

`datetime_add(`*기간* `,` *금액* `,` *datetime*`)`

## <a name="arguments"></a>인수

* `period`: [문자열](./scalar-data-types/string.md). 
* `amount`: [정수](./scalar-data-types/int.md)
* `datetime`: [datetime](./scalar-data-types/datetime.md) 값입니다.

가능한 *기간*값: 
- Year
- Quarter
- Month
- 주
- 일
- Hour
- 분
- Second
- Millisecond
- 마이크로초
- 나노초

## <a name="returns"></a>반환

특정 시간/날짜 간격이 추가 된 후의 날짜입니다.

## <a name="examples"></a>예제

```kusto
print  year = datetime_add('year',1,make_datetime(2017,1,1)),
quarter = datetime_add('quarter',1,make_datetime(2017,1,1)),
month = datetime_add('month',1,make_datetime(2017,1,1)),
week = datetime_add('week',1,make_datetime(2017,1,1)),
day = datetime_add('day',1,make_datetime(2017,1,1)),
hour = datetime_add('hour',1,make_datetime(2017,1,1)),
minute = datetime_add('minute',1,make_datetime(2017,1,1)),
second = datetime_add('second',1,make_datetime(2017,1,1))

```

|year|quarter|month|week|일|hour|minute|second|
|---|---|---|---|---|---|---|---|
|2018-01-01 00:00:00.0000000|2017-04-01 00:00:00.0000000|2017-02-01 00:00:00.0000000|2017-01-08 00:00:00.0000000|2017-01-02 00:00:00.0000000|2017-01-01 01:00:00.0000000|2017-01-01 00:01:00.0000000|2017-01-01 00:00:01.0000000|






