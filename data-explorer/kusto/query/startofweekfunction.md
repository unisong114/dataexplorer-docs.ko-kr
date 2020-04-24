---
title: startofweek() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기에서 startofweek()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 9705b586a0b8c5161b7d4c27735f644b6982c707
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81507228"
---
# <a name="startofweek"></a>startofweek()

제공된 경우 오프셋으로 이동된 날짜를 포함하는 주의 시작을 반환합니다.

요일의 시작은 일요일로 간주됩니다.

**구문**

`startofweek(`*날짜* `,`[*오프셋*]`)`

**인수**

* `date`: 입력 날짜입니다.
* `offset`: 입력 날짜로부터 의 오프셋 주(정수, 기본값 - 0)의 선택적 수입니다.

**반환**

*지정된* 날짜 값에 대한 주의 시작을 나타내는 날짜 시간(지정된 경우 오프셋)입니다.

**예제**

```kusto
  range offset from -1 to 1 step 1
 | project weekStart = startofweek(datetime(2017-01-01 10:10:17), offset) 
```

|주 시작|
|---|
|2016-12-25 00:00:00.0000000|
|2017-01-01 00:00:00.0000000|
|2017-01-08 00:00:00.0000000|