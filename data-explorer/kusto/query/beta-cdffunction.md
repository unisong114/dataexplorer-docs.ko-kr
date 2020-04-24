---
title: beta_cdf() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 beta_cdf()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 4faaeddc647d047755108f3c993db855a56de363
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81517921"
---
# <a name="beta_cdf"></a>beta_cdf()

표준 누적 베타 배포 함수를 반환합니다.

```kusto
beta_cdf(0.2, 10.0, 50.0)
```

*probability* = 확률`beta_cdf(`*x가*,... `)`그런 `beta_inv(`다음 *확률*,... `)` *x*x  = .

베타 분포는 일반적으로 샘플에 있는 특정 항목의 백분율 편차(예: 하루 중 TV를 시청하는 시간)를 연구하는 데 사용됩니다.

**구문**

`beta_cdf(`*x*`, `*알파*`, `*베타*`)`

**인수**

* *x*: 함수를 평가할 값입니다.
* *알파*: 분포의 매개 변수입니다.
* *베타*: 분포의 매개 변수입니다.

**반환**

* [누적 베타 배포 기능](https://en.wikipedia.org/wiki/Beta_distribution#Cumulative_distribution_function).

**참고 사항**

인수가 비숫자인 경우 beta_cdf()는 null 값을 반환합니다.

x< 0 또는 x > 1인 경우 beta_cdf())는 NaN 값을 반환합니다.

알파 ≤ 0 또는 베타 ≤ 0인 경우, beta_cdf())는 NaN 값을 반환한다.

**예**

```kusto
datatable(x:double, alpha:double, beta:double, comment:string)
[
    0.9, 10.0, 20.0, "Valid input",
    1.5, 10.0, 20.0, "x > 1, yields NaN",
    double(-10), 10.0, 20.0, "x < 0, yields NaN",
    0.1, double(-1.0), 20.0, "alpha is < 0, yields NaN"
]
| extend b = beta_cdf(x, alpha, beta)
```

|x|alpha|베타|주석|b|
|---|---|---|---|---|
|0.9|10|20|올바른 입력|0.999999999999959|
|1.5|10|20|x > 1, 출력량 NN|NaN|
|-10|10|20|x < 0, 출력량 NN|NaN|
|0.1|-1|20|알파는 0<, 수율 NaN|NaN|


**참고 항목**


* 베타 누적 확률 밀도 함수의 역계산을 위해 [베타-inv()](./beta-invfunction.md)을 참조하십시오.
* 확률 밀도 함수를 계산하는 경우 [베타-pdf()](./beta-pdffunction.md)를 참조하십시오.