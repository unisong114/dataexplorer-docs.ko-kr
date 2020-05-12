---
title: bin_at ()-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 bin_at ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 90055f644dbf653eb65546202832f7cab834a0ac
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83227600"
---
# <a name="bin_at"></a>bin_at()

Bin의 시작점에 대 한 제어를 사용 하 여 값을 고정 크기 "bin"으로 내림 합니다.
(참고 항목 [`bin function`](./binfunction.md) )

**구문**

`bin_at``(` *식* `,` *binsize* `, ` *fixedpoint*`)`

**인수**

* *Expression*: `datetime` 반올림할 값을 나타내는 숫자 형식 (및 포함)의 스칼라 식 `timespan` 입니다.
* *Binsize*: 각 bin의 크기를 나타내는 *식* 과 동일한 형식의 스칼라 상수입니다. 
* *Fixedpoint*: "fixed point" (즉, 값) 인 *식* 의 값 하나를 나타내는 *식* 과 동일한 형식의 스칼라 상수입니다 `fixed_point` `bin_at(fixed_point, bin_size, fixed_point) == fixed_point` .

**반환**

*식*아래에서 가장 근접 한 *binsize* 의 가장 가까운 배수 이며,이는 *fixedpoint* 가 자체로 변환 되도록 이동 합니다.

**예**

|식                                                                    |결과                           |설명                   |
|------------------------------------------------------------------------------|---------------------------------|---------------------------|
|`bin_at(6.5, 2.5, 7)`                                                         |`4.5`                            ||
|`bin_at(time(1h), 1d, 12h)`                                                   |`-12h`                           ||
|`bin_at(datetime(2017-05-15 10:20:00.0), 1d, datetime(1970-01-01 12:00:00.0))`|`datetime(2017-05-14 12:00:00.0)`|모든 bin이 정오에 될 예정입니다.   |
|`bin_at(datetime(2017-05-17 10:20:00.0), 7d, datetime(2017-06-04 00:00:00.0))`|`datetime(2017-05-14 00:00:00.0)`|모든 bin은 일요일에|


다음 예제에서 `"fixed point"` 인수는 bin 중 하나로 반환 되 고 다른 bin은에 따라 정렬 됩니다 `bin_size` . 또한 각 datetime bin은 해당 bin의 시작 시간을 나타냅니다.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
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
