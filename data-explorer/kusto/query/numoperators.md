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
ms.openlocfilehash: b79979abdc13d5cb6b7a71bde2301ed0169ae59b
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92249749"
---
# <a name="numerical-operators"></a>숫자 연산자

`int`, 및 형식은 `long` `real` 숫자 형식을 나타냅니다.
이러한 형식의 쌍 사이에는 다음 연산자를 사용할 수 있습니다.

연산자       |설명                         |예제
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

**모듈로 연산자에 대 한 설명**

두 숫자의 모듈로는 항상 Kusto를 "작은 음수가 아닌 숫자"로 반환 합니다.
따라서 두 숫자 *n*  %  *d*의 모듈로는 0 &le; (*n*  %  *d*) &lt; abs (*D*)가 있습니다.

예를 들어 다음 쿼리는 다음과 같습니다.

```kusto
print plusPlus = 14 % 12, minusPlus = -14 % 12, plusMinus = 14 % -12, minusMinus = -14 % -12
```

다음 결과를 생성 합니다.

|plusPlus  | minusPlus  | plusMinus  | minusMinus|
|----------|------------|------------|-----------|
|2         | 10         | 2          | 10        |