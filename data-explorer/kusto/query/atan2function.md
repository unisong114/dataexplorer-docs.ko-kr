---
title: atan2 ()-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 atan2 ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 1229ff1476afe2863f07cfc0ff7aecffadd5867f
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252755"
---
# <a name="atan2"></a>atan2()

양수 x 축과 원점에서 점 (y, x) 까지의 광선 사이의 각도를 라디안으로 계산 합니다.

## <a name="syntax"></a>구문

`atan2(`*y* `,` *x*`)`

## <a name="arguments"></a>인수

* *x*: x 좌표 (실수)
* *y*: y 좌표 (실수)입니다.

## <a name="returns"></a>반환

* 양의 x 축과 원점에서 점 (y, x) 까지의 광선 사이의 각도 (라디안)입니다.

## <a name="examples"></a>예

```kusto
print atan2_0 = atan2(1,1) // Pi / 4 radians (45 degrees)
| extend atan2_1 = atan2(0,-1) // Pi radians (180 degrees)
| extend atan2_2 = atan2(-1,0) // - Pi / 2 radians (-90 degrees)
```

|atan2_0|atan2_1|atan2_2|
|---|---|---|
|0.785398163397448|3.14159265358979|-1.5707963267949|