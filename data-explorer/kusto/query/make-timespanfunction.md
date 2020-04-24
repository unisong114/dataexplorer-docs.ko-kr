---
title: make_timespan() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 make_timespan()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 31301f684ea700cf89e9234c4c43adab068319b7
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81512770"
---
# <a name="make_timespan"></a>make_timespan()

지정된 기간에서 [시간 범위](./scalar-data-types/timespan.md) 스칼라 값을 만듭니다.

```kusto
make_timespan(1,12,30,55.123) == time(1.12:30:55.123)
```

**구문**

`make_timespan(`*시간*,*분*`)`

`make_timespan(`*시간*,*분,**두 번째*`)`

`make_timespan(`*일,**시간,**분,**두 번째*`)`

**인수**

* *일*: 일(양수 정수 값)
* *시간*: 시간(정수 값, 0에서 23까지)
* *분*: 분(정수 값, 0에서 59까지)
* *두 번째*: 두 번째 (실제 값, 0에서 59.99999999)

**반환**

생성에 성공하면 결과는 [시간 범위](./scalar-data-types/timespan.md) 값이 되고 그렇지 않으면 결과가 null이 됩니다.
 
**예제**

```kusto
print ['timespan'] = make_timespan(1,12,30,55.123)

```

|timespan|
|---|
|1.12:30:55.1230000|


