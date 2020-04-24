---
title: beta_pdf() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 beta_pdf()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 53b86d88b05cc6c5cc31f1e3bbb9e3e712eed7f8
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81517870"
---
# <a name="beta_pdf"></a>beta_pdf()

확률 밀도 베타 함수를 반환합니다.

```kusto
beta_pdf(0.2, 10.0, 50.0)
```

베타 분포는 일반적으로 샘플에 있는 특정 항목의 백분율 편차(예: 하루 중 TV를 시청하는 시간)를 연구하는 데 사용됩니다.

**구문**

`beta_pdf(`*x*`, `*알파*`, `*베타*`)`

**인수**

* *x*: 함수를 평가할 값입니다.
* *알파*: 분포의 매개 변수입니다.
* *베타*: 분포의 매개 변수입니다.

**반환**

* [확률 베타 밀도 함수](https://en.wikipedia.org/wiki/Beta_distribution#Probability_density_function).

**참고 사항**

인수가 비숫자인 경우 beta_pdf()는 null 값을 반환합니다.

x ≤ 0 또는 1 ≤ x인 경우 beta_pdf())는 NaN 값을 반환합니다.

알파 ≤ 0 또는 베타 ≤ 0인 경우, beta_pdf())는 NaN 값을 반환합니다.

**예**

```kusto
datatable(x:double, alpha:double, beta:double, comment:string)
[
    0.5, 10.0, 20.0, "Valid input",
    1.5, 10.0, 20.0, "x > 1, yields NaN",
    double(-10), 10.0, 20.0, "x < 0, yields NaN",
    0.1, double(-1.0), 20.0, "alpha is < 0, yields NaN"
]
| extend r = beta_pdf(x, alpha, beta)
```

|x|alpha|베타|주석|r|
|---|---|---|---|---|
|0.5|10|20|올바른 입력|0.746176019310951|
|1.5|10|20|x > 1, 출력량 NN|NaN|
|-10|10|20|x < 0, 출력량 NN|NaN|
|0.1|-1|20|알파는 0<, 수율 NaN|NaN|

**참조**

* 베타 누적 확률 밀도 함수의 역계산을 위해 [베타-inv()](./beta-invfunction.md)을 참조하십시오.
* 표준 누적 베타 배포 기능에 대한 [베타-cdf()](./beta-cdffunction.md)를 참조하십시오.