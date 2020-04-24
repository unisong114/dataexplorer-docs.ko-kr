---
title: case() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 case()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 479c4a99d410a2df7a608531914d7dccfc555e7e
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81517275"
---
# <a name="case"></a>case()

조건자 목록을 평가하고 조건어가 충족되는 첫 번째 결과 식을 반환합니다.

조건자 중 어느 것도 `true`반환되지 않으면 마지막 식(the)의 `else`결과가 반환됩니다.
모든 홀수 인수(개수가 1에서 시작)는 `boolean` 값을 평가하는 식이어야 합니다.
모든 짝수 `then`인수(들)와 마지막 인수(the)는 `else`동일한 형식이어야 합니다.

**구문**

`case(`*predicate_1* `,` *then_1*, *predicate_2* `,` *then_2*, *predicate_3* `,` *then_3* *else*`)`

**인수**

* *predicate_i*: 값을 평가하는 식입니다. `boolean`
* *then_i*: *predicate_i* 평가하는 첫 번째 조건자인 경우 평가되고 해당 값이 함수에서 `true`반환되는 식입니다.
* *else*: *predicate_i* 평가하지 않는 경우 평가되고 해당 값이 함수에서 `true`반환되는 식입니다.

**반환**

predicate_i *평가되는* `true`첫 *번째 then_i* 값 또는 조건자 중 어느 것도 충족되지 않는 경우 *다른* 값입니다.

**예제**

```kusto
range Size from 1 to 15 step 2
| extend bucket = case(Size <= 3, "Small", 
                       Size <= 10, "Medium", 
                       "Large")
```

|크기|bucket|
|---|---|
|1|작음|
|3|작음|
|5|중간|
|7|중간|
|9|중간|
|11|큰|
|13|큰|
|15|큰|
