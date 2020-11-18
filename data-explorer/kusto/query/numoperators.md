---
title: 숫자 연산자-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 숫자 연산자에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: e020d5f6692c8c290b6a68b2ed1b52bc710c89c8
ms.sourcegitcommit: 574296b9a84084de031684a65f32b6c1bd1a4858
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94713992"
---
# <a name="numerical-operators"></a>숫자 연산자

`int`, 및 형식은 `long` `real` 숫자 형식을 나타냅니다.
이러한 형식의 쌍 사이에는 다음 연산자를 사용할 수 있습니다.

연산자       |Description                         |예제
---------------|------------------------------------|-----------------------
`+`            |추가                                 |`3.14 + 3.14`, `ago(5m) + 5m`
`-`            |빼기                            |`0.23 - 0.22`,
`*`            |곱하기                            |`1s * 5`, `2 * 2`
`/`            |나누기                              |`10m / 1s`, `4 / 2`
`%`            |나머지값                              |`4 % 2`
`<`            |보다 작음                                |`1 < 10`, `10sec < 1h`, `now() < datetime(2100-01-01)`
`>`            |보다 큼                             |`0.23 > 0.22`, `10min > 1sec`, `now() > ago(1d)`
`==`           |같음                              |`1 == 1`
`!=`           |같지 않음                          |`1 != 0`
`<=`           |작거나 같음                       |`4 <= 5`
`>=`           |크거나 같음                    |`5 >= 4`
`in`           |요소 중 하나와 같음       |[여기를 참조 하세요.](inoperator.md)
`!in`          |어떤 요소와도 같지 않음   |[여기를 참조 하세요.](inoperator.md)

> [!NOTE]
> 한 숫자 형식에서 다른 숫자 형식으로 변환 하려면 `to*()` 함수를 사용 합니다. 예를 들어 및을 참조 하십시오 [`tolong()`](tolongfunction.md) [`toint()`](tointfunction.md) .

**모듈로 연산자에 대 한 설명**

두 숫자의 모듈로는 항상 Kusto를 "작은 음수가 아닌 숫자"로 반환 합니다.
따라서 두 숫자 *n*  %  *d* 의 모듈로는 0 &le; (*n*  %  *d*) &lt; abs (*D*)가 있습니다.

예를 들어 다음 쿼리는 다음과 같습니다.

```kusto
print plusPlus = 14 % 12, minusPlus = -14 % 12, plusMinus = 14 % -12, minusMinus = -14 % -12
```

다음 결과를 생성 합니다.

|plusPlus  | minusPlus  | plusMinus  | minusMinus|
|----------|------------|------------|-----------|
|2         | 10         | 2          | 10        |
