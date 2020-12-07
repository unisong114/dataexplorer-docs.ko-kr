---
title: dcount()(집계 함수) - Azure Data Explorer
description: 이 문서에서는 Azure Data Explorer의 dcount()(집계 함수)에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.localizationpriority: high
ms.openlocfilehash: 947ab0af6a5aaa98bb07b08005b940fdf2ce6ae5
ms.sourcegitcommit: f49e581d9156e57459bc69c94838d886c166449e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2020
ms.locfileid: "95513185"
---
# <a name="dcount-aggregation-function"></a>dcount()(집계 함수)

요약 그룹의 스칼라 식에서 가져온 고유 값 수에 대한 추정 값을 반환합니다.

> [!NOTE]
> `dcount()` 집계 함수는 주로 대형 집합의 카디널리티를 추정하는 데 유용합니다. 정확도를 높이기 위해 성능을 향상시킬 수 있으며 실행마다 다른 결과를 반환할 수 있습니다. 입력 순서는 출력에 영향을 미칠 수 있습니다.

## <a name="syntax"></a>Syntax

... `|` `summarize` `dcount` `(`*`Expr`*[, *`Accuracy`*]`)` ...

## <a name="arguments"></a>인수

* *Expr*: 고유한 값을 계산할 스칼라 식입니다.
* *정확도*: 요청된 추정 정확도를 정의하는 선택적 `int` 리터럴입니다. 지원되는 값은 아래를 참조하세요. 지정하지 않으면 기본값 `1`이 사용됩니다.

## <a name="returns"></a>반환

그룹에 있는 *`Expr`* 의 고유 값 수에 대한 추정치를 반환합니다.

## <a name="example"></a>예제

```kusto
PageViewLog | summarize countries=dcount(country) by continent
```

:::image type="content" source="images/dcount-aggfunction/dcount.png" alt-text="D 개수":::

`G`별로 그룹화된 `V` 고유 값의 정확한 개수를 가져옵니다.

```kusto
T | summarize by V, G | summarize count() by G
```

`V`의 고유 값에 `G`의 고유 값 수를 곱하기 때문에 이 계산에는 상당한 양의 내부 메모리가 필요합니다.
이로 인해 메모리 오류가 발생하거나 실행 시간이 길어질 수 있습니다. 
`dcount()`빠르고 안정적인 대안을 제공합니다.

```kusto
T | summarize dcount(B) by G | count
```

## <a name="estimation-accuracy"></a>추정 정확도

`dcount()` 집계 함수는 설정 카디널리티의 추계 예측을 수행하는 [HLL(HyperLogLog) 알고리즘](https://en.wikipedia.org/wiki/HyperLogLog)의 변형을 사용합니다. 알고리즘은 메모리 크기당 정확도와 실행 시간의 균형을 맞추는 데 사용할 수 있는 "노브"를 제공합니다.

|정확도|오류(%)|항목 수   |
|--------|---------|--------------|
|       0|      1.6|2<sup>12</sup>|
|       1|      0.8|2<sup>14</sup>|
|       2|      0.4|2<sup>16</sup>|
|       3|     0.28|2<sup>17</sup>|
|       4|      0.2|2<sup>18</sup>|

> [!NOTE]
> "항목 수" 열은 HLL 구현에서 1바이트 카운터의 수입니다.

알고리즘에는 설정 카디널리티가 충분히 작을 경우 완전한 개수(오류 0개)를 수행하기 위한 몇 가지 프로비전이 포함됩니다.
* 정확도 수준이 `1`이면 1000개의 값이 반환됩니다.
* 정확도 수준이 `2`이면 8000개의 값이 반환됩니다.

바인딩된 오류는 이론적 바운드가 아닌 확률입니다. 값은 오차 분포의 표준 편차(시그마)이며, 추정치의 99.7%는 3 x 시그마 미만의 상대 오차를 갖습니다.

다음 이미지는 지원되는 모든 정확도 설정에 대한 상대적 추정 오차의 확률 분포 함수를 백분율로 보여줍니다.

:::image type="content" border="false" source="images/dcount-aggfunction/hll-error-distribution.png" alt-text="hll 오류 배포":::
