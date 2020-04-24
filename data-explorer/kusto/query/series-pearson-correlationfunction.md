---
title: series_pearson_correlation() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 series_pearson_correlation()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/31/2019
ms.openlocfilehash: 6454ec528e7a9e53b2feab5a7fefa1236ed80cdf
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81508112"
---
# <a name="series_pearson_correlation"></a>series_pearson_correlation()

두 개의 숫자 계열 입력의 pearson 상관 계수를 계산합니다.

참조: [피어슨 상관 계수](https://en.wikipedia.org/wiki/Pearson_correlation_coefficient).

**구문**

`series_pearson_correlation(`*시리즈1* `,` *시리즈2*`)`

**인수**

* *Series1, Series2*: 상관 계수를 계산하기 위한 입력 숫자 배열입니다. 모든 인수는 동일한 길이의 동적 배열이어야 합니다. 

**반환**

두 입력 간의 계산된 Pearson 상관 계수입니다. 숫자가 아닌 요소 또는 존재하지 않는 요소(다른 크기의 배열)는 `null` 결과를 생성합니다.

**예제**

```kusto
range s1 from 1 to 5 step 1 | extend s2 = 2*s1 // Perfect correlation
| summarize s1 = make_list(s1), s2 = make_list(s2)
| extend correlation_coefficient = series_pearson_correlation(s1,s2)
```

|s1|s2|correlation_coefficient|
|---|---|---|
|[1,2,3,4,5]|[2,4,6,8,10]|1|
