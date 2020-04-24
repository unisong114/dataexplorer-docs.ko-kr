---
title: atan2() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기에서 atan2()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: c8b49191c9d955cf5a91bde2032798f4703f7910
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81518397"
---
# <a name="atan2"></a>atan2()

양수 x축과 원점에서 점(y, x)까지의 광선 사이의 각도(라디안)를 계산합니다.

**구문**

`atan2(`*y*`,`*x*`)`

**인수**

* *x*: X 좌표 (실제 숫자).
* *y*: Y 좌표 (실제 숫자).

**반환**

* 양수 x축과 원점에서 점(y, x)까지의 광선 사이의 각도(라디안)입니다.

**예**

```kusto
print atan2_0 = atan2(1,1) // Pi / 4 radians (45 degrees)
| extend atan2_1 = atan2(0,-1) // Pi radians (180 degrees)
| extend atan2_2 = atan2(-1,0) // - Pi / 2 radians (-90 degrees)
```

|atan2_0|atan2_1|atan2_2|
|---|---|---|
|0.785398163397448|3.14159265358979|-1.5707963267949|