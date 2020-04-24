---
title: iif() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 iif()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 36ba98b9677055dffce32911d80e67a9161b673b
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81514045"
---
# <a name="iif"></a>iif()

첫 번째 인수(술어)를 평가하고 술어가 `true` (두 번째) 또는 `false` (세 번째)로 평가되는지 여부에 따라 두 번째 또는 세 번째 인수의 값을 반환합니다.

두 번째 및 세 번째 인수는 동일한 형식이어야 합니다.

**구문**

`iif(`*조건자* `,` *ifTrue* `,` *ifFalse*`)`

**인수**

* *조건자*: 값을 평가하는 식입니다. `boolean`
* *ifTrue*: *조건어가* `true`평가되는 경우 평가되는 식과 함수에서 해당 값이 반환됩니다.
* *ifFalse*: *조건자* 평가하는 경우 평가되는 식과 함수에서 `false`해당 값이 반환됩니다.

**반환**

이 함수는 *predicate*이 `true`(으)로 계산되는 경우 *ifTrue*의 값을 반환하며 그렇지 않은 경우 *ifFalse*의 값을 반환합니다.

**예제**

```kusto
T 
| extend day = iif(floor(Timestamp, 1d)==floor(now(), 1d), "today", "anotherday")
```

에 대한 [`iff()`](ifffunction.md)별칭입니다.