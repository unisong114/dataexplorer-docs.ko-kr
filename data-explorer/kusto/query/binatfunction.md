---
title: bin_at() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기에서 bin_at()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 218142e1c377e6a72abde154d4576025698b2f0d
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81517445"
---
# <a name="bin_at"></a>bin_at()

Bin의 시작점을 제어하여 고정 크기 "bin"으로 값을 반올림합니다.
(참조 [`bin function`](./binfunction.md).)

**구문**

`bin_at``(` *표현식* `,` *BinSize* `, ` *고정점*`)`

**인수**

* *표현식*: 둥근 값을 나타내는 숫자 형식(포함 `datetime` 및 `timespan`포함)의 스칼라 식입니다.
* *BinSize*: 각 저장소의 크기를 나타내는 *식과* 동일한 유형의 스칼라 상수입니다. 
* *FixedPoint*: "고정점"(즉, `fixed_point` `bin_at(fixed_point, bin_size, fixed_point) == fixed_point`값)인 식의 한 값을 나타내는 *식과* 동일한 형식의 스칼라 상수입니다. *Expression*

**반환**

*표현식*아래의 *BinSize의* 가장 가까운 배수는 *FixedPoint가* 그 자체로 변환되도록 이동되었습니다.

**예**

|식                                                                    |결과                           |주석                   |
|------------------------------------------------------------------------------|---------------------------------|---------------------------|
|`bin_at(6.5, 2.5, 7)`                                                         |`4.5`                            ||
|`bin_at(time(1h), 1d, 12h)`                                                   |`-12h`                           ||
|`bin_at(datetime(2017-05-15 10:20:00.0), 1d, datetime(1970-01-01 12:00:00.0))`|`datetime(2017-05-14 12:00:00.0)`|모든 쓰레기통은 정오에 있을 것입니다.   |
|`bin_at(datetime(2017-05-17 10:20:00.0), 7d, datetime(2017-06-04 00:00:00.0))`|`datetime(2017-05-14 00:00:00.0)`|모든 쓰레기통은 일요일에 있을 것입니다.|


다음 예제에서는 arg가 `"fixed point"` 저장소 중 하나로 반환되고 다른 저장소가 `bin_size`에 따라 정렬됩니다. 또한 각 datetime 저장소는 해당 저장소의 시작 시간을 나타냅니다.

```kusto

datatable(Date:datetime, Num:int)[
datetime(2018-02-24T15:14),3,
datetime(2018-02-23T16:14),4,
datetime(2018-02-26T15:14),5]
| summarize sum(Num) by bin_at(Date, 1d, datetime(2018-02-24 15:14:00.0000000)) 
```

|Date|sum_Num|
|---|---|
|2018-02-23 15:14:00.0000000|4|
|2018-02-24 15:14:00.0000000|3|
|2018-02-26 15:14:00.0000000|5|