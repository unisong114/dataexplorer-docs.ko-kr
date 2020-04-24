---
title: 숫자 연산자 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 숫자 연산에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 6667005960152814be952d93fe932b4971d5322b
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81512022"
---
# <a name="numerical-operators"></a>숫자 연산자

및 `int` `long`숫자 형식을 `real` 나타냅니다.
다음 연산자는 다음 유형의 쌍 간에 사용할 수 있습니다.

연산자       |Description                         |예제
---------------|------------------------------------|-----------------------
`+`            |추가                                 |`3.14 + 3.14`, `ago(5m) + 5m`
`-`            |빼기                            |`0.23 - 0.22`,
`*`            |곱하기                            |`1s * 5`, `2 * 2`
`/`            |나누기                              |`10m / 1s`, `4 / 2`
`%`            |모듈로                              |`4 % 2`
`<`            |더 작음                                |`1 < 10`, `10sec < 1h`, `now() < datetime(2100-01-01)`
`>`            |더 큼                             |`0.23 > 0.22`, `10min > 1sec`, `now() > ago(1d)`
`==`           |같음                              |`1 == 1`
`!=`           |같지 않음                          |`1 != 0`
`<=`           |작거나 같음                       |`4 <= 5`
`>=`           |크거나 같음                    |`5 >= 4`
`in`           |요소 중 하나와 같음       |[여기를 참조하십시오.](inoperator.md)
`!in`          |어떤 요소와도 같지 않음   |[여기를 참조하십시오.](inoperator.md)

**조절연산자에 대한 코멘트**

두 숫자의 조절은 항상 쿠스토에서 "작은 비음수"로 돌아갑니다.
따라서, 두 개의 숫자, *N* % *D의*조절은 &le; 다음과 같이: &lt; 0 *(ND)* % *D*복근(D).*D*

예를 들어 다음 쿼리는 다음과 같은 것입니다.

```kusto
print plusPlus = 14 % 12, minusPlus = -14 % 12, plusMinus = 14 % -12, minusMinus = -14 % -12
```

이 결과를 생성합니다.

|플러스 플러스  | 마이너스 플러스  | 플러스 마이너스  | 마이너스 마이너스|
|----------|------------|------------|-----------|
|2         | 10         | 2          | 10        |