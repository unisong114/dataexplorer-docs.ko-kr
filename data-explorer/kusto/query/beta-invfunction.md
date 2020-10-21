---
title: beta_inv ()-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기에서 beta_inv ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 4e302f997addbdc4f2f9da4bd906844dbd1d07a4
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92247999"
---
# <a name="beta_inv"></a>beta_inv()

베타 누적 확률 베타 밀도 함수의 역을 반환 합니다.

```kusto
beta_inv(0.1, 10.0, 50.0)
```

*확률*  =  `beta_cdf(` *x*,... 이면 `)` `beta_inv(` *확률*,... `)`  =  *x*. 

예상 완료 시간과 가변성이 주어질 경우, 프로젝트 계획에 베타 분포를 사용하여 가능한 완료 시간을 모델링할 수 있습니다.

## <a name="syntax"></a>구문

`beta_inv(`*확률* `, ` *알파* `, ` *베타*`)`

## <a name="arguments"></a>인수

* *확률*: 베타 분포와 관련 된 확률입니다.
* *alpha*: 분포의 매개 변수입니다.
* *beta*: 분포의 매개 변수입니다.

## <a name="returns"></a>반환

* 베타 누적 확률 밀도 함수 [beta_cdf ()](./beta-cdffunction.md) 의 역입니다.

**참고**

숫자가 아닌 인수가 있는 경우 beta_inv ()는 null 값을 반환 합니다.

Alpha ≤ 0 또는 beta ≤ 0 인 경우 beta_inv ()는 null 값을 반환 합니다.

확률이 ≤ 0 또는 probability > 1 인 경우 beta_inv ()는 NaN 값을 반환 합니다.

확률의 값이 지정 된 경우 () beta_inv ()는 beta_cdf (x, alpha, beta) = 확률 등 x 값을 검색 합니다.

## <a name="examples"></a>예

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(p:double, alpha:double, beta:double, comment:string)
[
    0.1, 10.0, 20.0, "Valid input",
    1.5, 10.0, 20.0, "p > 1, yields null",
    0.1, double(-1.0), 20.0, "alpha is < 0, yields NaN"
]
| extend b = beta_inv(p, alpha, beta)
```

|p|alpha|베타|주석|b|
|---|---|---|---|---|
|0.1|10|20|유효한 입력|0.226415022388749|
|1.5|10|20|p > 1, null 생성||
|0.1|-1|20|alpha는 0 < NaN을 생성 합니다.|NaN|

## <a name="see-also"></a>참조

* 누적 베타 배포 함수를 계산 하려면 [베타-cdf ()](./beta-cdffunction.md)를 참조 하세요.
* 계산 확률 베타 밀도 함수는 [beta-pdf ()](./beta-pdffunction.md)를 참조 하세요.
