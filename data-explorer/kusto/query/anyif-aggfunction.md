---
title: anyif() (집계 함수) - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기에서 anyif(집계 함수)에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 813df821bad1b7e57315dad9bcd7b1387a2cd678
ms.sourcegitcommit: 29018b3db4ea7d015b1afa65d49ecf918cdff3d6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2020
ms.locfileid: "82030085"
---
# <a name="anyif-aggregation-function"></a>anyif(집계 함수)

조건자가 true인 [요약 연산자의](summarizeoperator.md) 각 그룹에 대해 임의로 하나의 레코드를 선택하고 이러한 각 레코드에 대한 식 값을 반환합니다.

**구문**

`summarize`예절, *조건자)'* *Expr* `anyif` `(`

**인수**

* *예:* 반환할 입력에서 선택한 각 레코드에 대한 식입니다.
* *술어*: 평가를 위해 고려될 수 있는 레코드를 나타내는 술어입니다.

**반환**

집계 `anyif` 함수는 요약 연산자의 각 그룹에서 임의로 선택한 각 레코드에 대해 계산된 식값을 반환합니다. *조건어가* true를 반환하는 레코드만 선택할 수 있습니다(조건어가 true를 반환하지 않으면 null 값이 생성됨).

**주의**

이 함수는 일부 조건어가 true인 경우 복합 그룹 키의 값당 하나의 열의 샘플 값을 얻으려는 경우에 유용합니다.

함수는 이러한 값이 있는 경우 null/비빈 이값을 반환하려고 시도합니다.

**예**

3억에서 6억까지의 인구가 있는 무작위 대륙을 표시합니다.

```kusto
Continents | summarize anyif(Continent, Population between (300000000 .. 600000000))
```

:::image type="content" source="images/aggfunction/any1.png" alt-text="모든 1":::
