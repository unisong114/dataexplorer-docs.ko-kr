---
title: beta_inv() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 beta_inv()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 20bdf8bfc01ef3ac6c6a12f6a43d87fd7b5c07e6
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81517887"
---
# <a name="beta_inv"></a>beta_inv()

베타 누적 확률 베타 밀도 함수의 역버전을 반환합니다.

```kusto
beta_inv(0.1, 10.0, 50.0)
```

*probability* = 확률`beta_cdf(`*x가*,... `)`그런 `beta_inv(`다음 *확률*,... `)` *x*x  = . 

예상 완료 시간과 가변성이 주어질 경우, 프로젝트 계획에 베타 분포를 사용하여 가능한 완료 시간을 모델링할 수 있습니다.

**구문**

`beta_inv(`*확률*`, `*알파*`, `*베타*`)`

**인수**

* *확률*: 베타 분포와 관련된 확률입니다.
* *알파*: 분포의 매개 변수입니다.
* *베타*: 분포의 매개 변수입니다.

**반환**

* 베타 누적 확률 밀도 함수의 [역beta_cdf()](./beta-cdffunction.md)

**참고 사항**

인수가 비숫자인 경우 beta_inv()는 null 값을 반환합니다.

알파 ≤ 0 또는 베타 ≤ 0인 경우 beta_inv())는 null 값을 반환합니다.

확률 ≤ 0 또는 > 1 의 확률인 경우 beta_inv()는 NaN 값을 반환합니다.

확률값을 감안할 때 beta_inv()는 beta_cdf(x, 알파, 베타) = 확률과 같은 값 x를 찾습니다.

**예**

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
|0.1|10|20|올바른 입력|0.226415022388749|
|1.5|10|20|p > 1, null 산출||
|0.1|-1|20|알파는 0<, 수율 NaN|NaN|

**참고 항목**

* 누적 베타 배포 기능을 계산하는 경우 [베타-cdf()](./beta-cdffunction.md)를 참조하십시오.
* 확률 베타 밀도 함수를 계산하는 경우 [베타 pdf()](./beta-pdffunction.md)를 참조하십시오.