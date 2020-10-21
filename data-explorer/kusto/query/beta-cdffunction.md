---
title: beta_cdf ()-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 beta_cdf ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 16365c7445ca5436098028cbfbe5709e5b425a38
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92253096"
---
# <a name="beta_cdf"></a>beta_cdf()

표준 누적 베타 분포 함수를 반환 합니다.

```kusto
beta_cdf(0.2, 10.0, 50.0)
```

*확률*  =  `beta_cdf(` *x*,... 이면 `)` `beta_inv(` *확률*,... `)`  =  *x*.

베타 분포는 일반적으로 샘플에 있는 특정 항목의 백분율 편차(예: 하루 중 TV를 시청하는 시간)를 연구하는 데 사용됩니다.

## <a name="syntax"></a>구문

`beta_cdf(`*x* `, ` *알파* `, ` *베타*`)`

## <a name="arguments"></a>인수

* *x*: 함수를 계산할 값입니다.
* *alpha*: 분포의 매개 변수입니다.
* *beta*: 분포의 매개 변수입니다.

## <a name="returns"></a>반환

* [누적 베타 분포 함수](https://en.wikipedia.org/wiki/Beta_distribution#Cumulative_distribution_function)입니다.

**참고**

숫자가 아닌 인수가 있는 경우 beta_cdf ()는 null 값을 반환 합니다.

X < 0 또는 x > 1 인 경우 beta_cdf ()은 NaN 값을 반환 합니다.

Alpha ≤ 0 또는 alpha > 1만 beta_cdf ()은 NaN 값을 반환 합니다.

Beta ≤ 0 또는 beta > 1만 인 경우 beta_cdf ()는 NaN 값을 반환 합니다.

## <a name="examples"></a>예

<!-- csl: https://help.kusto.windows.net/Samples -->
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
|0.9|10|20|유효한 입력|0.999999999999959|
|1.5|10|20|x > 1, NaN 생성|NaN|
|-10|10|20|x < 0, NaN 생성|NaN|
|0.1|-1|20|alpha는 0 < NaN을 생성 합니다.|NaN|


## <a name="see-also"></a>참조


* 베타 누적 확률 밀도 함수의 역함수를 계산 하려면 [beta-inv ()](./beta-invfunction.md)를 참조 하세요.
* 확률 밀도 함수를 계산 하려면 [beta-pdf ()](./beta-pdffunction.md)를 참조 하세요.
