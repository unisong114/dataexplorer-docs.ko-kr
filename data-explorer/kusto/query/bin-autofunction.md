---
title: bin_auto() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 bin_auto()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: ebb214ae6a2676bf59a37e1e4e9cc3c085374bb3
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81517836"
---
# <a name="bin_auto"></a>bin_auto()

쿼리 속성에서 제공하는 bin 크기와 시작 점을 제어하여 고정 크기 "bin"으로 값을 반올림합니다.

**구문**

`bin_auto``(` *표현식*`)`

**인수**

* *표현식*: 둥근 값을 나타내는 숫자 형식의 스칼라 식입니다.

**클라이언트 요청 속성**

* `query_bin_auto_size`: 각 휴지통의 크기를 나타내는 숫자 리터럴입니다.
* `query_bin_auto_at`: "고정점"인 *Expression의* 한 값을 나타내는 숫자 리터럴(즉, `fixed_point` `bin_auto(fixed_point)` == `fixed_point`값입니다.)

**반환**

아래 `query_bin_auto_at` *표현식의*가장 가까운 배수는 `query_bin_auto_at` 그 자체로 변환되도록 이동되었습니다.

**예**

```kusto
set query_bin_auto_size=1h;
set query_bin_auto_at=datetime(2017-01-01 00:05);
range Timestamp from datetime(2017-01-01 00:05) to datetime(2017-01-01 02:00) step 1m
| summarize count() by bin_auto(Timestamp)
```

|타임스탬프                    | count_|
|-----------------------------|-------|
|2017-01-01 00:05:00.0000000  | 60    |
|2017-01-01 01:05:00.0000000  | 56    |