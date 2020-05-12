---
title: beta_pdf ()-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 beta_pdf ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 8ebd4cb0ab8a5bffec717f83892a3ea11b35f409
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83227641"
---
# <a name="beta_pdf"></a>beta_pdf()

확률 밀도 베타 함수를 반환 합니다.

```kusto
beta_pdf(0.2, 10.0, 50.0)
```

베타 분포는 일반적으로 샘플에 있는 특정 항목의 백분율 편차(예: 하루 중 TV를 시청하는 시간)를 연구하는 데 사용됩니다.

**구문**

`beta_pdf(`*x* `, ` *알파* `, ` *베타*`)`

**인수**

* *x*: 함수를 계산할 값입니다.
* *alpha*: 분포의 매개 변수입니다.
* *beta*: 분포의 매개 변수입니다.

**반환**

* [확률 베타 밀도 함수](https://en.wikipedia.org/wiki/Beta_distribution#Probability_density_function)입니다.

**참고 사항**

숫자가 아닌 인수가 있는 경우 beta_pdf ()는 null 값을 반환 합니다.

X ≤ 0 또는 1 ≤ x 인 경우 beta_pdf ()은 NaN 값을 반환 합니다.

Alpha ≤ 0 또는 beta ≤ 0 인 경우 beta_pdf ()은 NaN 값을 반환 합니다.

**예**

<!-- csl: https://help.kusto.windows.net/Samples -->
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
|0.5|10|20|유효한 입력|0.746176019310951|
|1.5|10|20|x > 1, NaN 생성|NaN|
|-10|10|20|x < 0, NaN 생성|NaN|
|0.1|-1|20|alpha는 0 < NaN을 생성 합니다.|NaN|

**참조**

* 베타 누적 확률 밀도 함수의 역함수를 계산 하려면 [beta-inv ()](./beta-invfunction.md)를 참조 하세요.
* 표준 누적 베타 배포 함수는 [베타-cdf ()](./beta-cdffunction.md)를 참조 하세요.
