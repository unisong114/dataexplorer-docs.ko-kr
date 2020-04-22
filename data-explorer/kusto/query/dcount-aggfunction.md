---
title: dcount() (집계 함수) - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기에서 dcount() (집계 함수)에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 6f1df8c93d21b73be3753468708a119177d4d602
ms.sourcegitcommit: 29018b3db4ea7d015b1afa65d49ecf918cdff3d6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2020
ms.locfileid: "82030418"
---
# <a name="dcount-aggregation-function"></a>dcount() (집계 함수)

요약 그룹의 스칼라 식에서 가져온 고유 값 수에 대한 예상값을 반환합니다.

**구문**

... `|` `,` *Accuracy*`)` *Expr* Expr [ 정확도 ] ... `summarize` `dcount` `(`

**인수**

* *Expr*: 고유한 값을 계산해야 하는 스칼라 식입니다.
* *정확도*: `int` 요청된 추정 정확도를 정의하는 선택적 리터럴입니다. 지원되는 값은 아래를 참조하십시오. 지정되지 않은 경우 `1` 기본값이 사용됩니다.

**반환**

그룹에 있는 *Expr* 의 고유 값 수에 대한 추정치를 반환합니다.

**예제**

```kusto
PageViewLog | summarize countries=dcount(country) by continent
```

:::image type="content" source="images/dcount-aggfunction/dcount.png" alt-text="D 카운트":::

**참고 사항**

`dcount()` 집계 함수는 주로 거대한 집합의 카디널리티를 추정하는 데 유용합니다. 성능은 정확도를 위해 거래되므로 실행마다 다른 결과를 반환할 수 있습니다(입력 순서가 출력에 영향을 줄 수 있음).

그룹화된 고유 값의 `V` 정확한 개수를 얻으려면 다음을 수행합니다. `G`

```kusto
T | summarize by V, G | summarize count() by G
```

이 계산에는 고유 값에 고유한 `V` 값과 고유 값의 수를 `G`곱하기 때문에 많은 내부 메모리가 필요합니다. 따라서 메모리 오류 또는 큰 실행 시간이 발생할 수 있습니다. `dcount()`빠르고 신뢰할 수 있는 대안을 제공합니다.

```kusto
T | summarize dcount(B) by G | count
```

## <a name="estimation-accuracy"></a>추정 정확도

집계 `dcount()` 함수는 [HLL(HyperLogLog) 알고리즘의](https://en.wikipedia.org/wiki/HyperLogLog)변형을 사용하며, 이 알고리즘은 설정된 카디널리티의 최신 추정을 수행합니다. 알고리즘은 정확도와 실행 시간 / 메모리 크기의 균형을 유지하는 데 사용할 수있는 "손잡이"를 제공합니다 :

|정확도|오류(%)|항목 수   |
|--------|---------|--------------|
|       0|      1.6|2<sup>12</sup>|
|       1|      0.8|2<sup>14</sup>|
|       2|      0.4|2<sup>16</sup>|
|       3|     0.28|2<sup>17</sup>|
|       4|      0.2|2<sup>18</sup>|

> [!NOTE]
> "항목 수" 열은 HLL 구현에서 1바이트 카운터의 수입니다.

알고리즘에는 설정된 카디널리티가 충분히 작은 경우 완벽한 개수(제로 오차)를 수행하기 위한 몇 가지 `1`조항이 포함되어 있습니다(정확도 수준이 `2`1000값인 경우 1000 값, 정확도 수준이 8000값인 경우).

오류 바인딩은 이론적 바운드가 아니라 확률적입니다. 이 값은 오류 분포(시그마)의 표준 편차이며, 99.7%의 추정치는 시그마의 상대적 오차를 가지게 됩니다.

다음은 지원되는 모든 정확도 설정에 대한 상대 추정 오차(백분율)의 확률 분포 함수를 나타냅니다.

:::image type="content" border="false" source="images/dcount-aggfunction/hll-error-distribution.png" alt-text="hll 오류 분포":::
