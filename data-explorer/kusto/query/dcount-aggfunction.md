---
title: dcount () (집계 함수)-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 dcount () (집계 함수)에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 45ab913fdc659444ac578ca725e2afb24256a38b
ms.sourcegitcommit: 3dfaaa5567f8a5598702d52e4aa787d4249824d4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/05/2020
ms.locfileid: "87803712"
---
# <a name="dcount-aggregation-function"></a>dcount () (집계 함수)

요약 그룹에서 스칼라 식에 사용 되는 고유 값 수에 대 한 예상 값을 반환 합니다.

> [!NOTE]
> `dcount()`집계 함수는 주로 큰 집합의 카디널리티를 추정 하는 데 유용 합니다. 정확도를 높이기 위해 성능을 향상 시킬 수 있으며 실행 마다 다른 결과를 반환할 수 있습니다. 입력 순서는 출력에 영향을 미칠 수 있습니다.

## <a name="syntax"></a>구문

... `|` `summarize` `dcount` `(`*`Expr`*[, *`Accuracy`*]`)` ...

## <a name="arguments"></a>인수

* *Expr*: 고유 값을 계산할 스칼라 식입니다.
* *정확도*: 요청 된 `int` 예측 정확도를 정의 하는 선택적 리터럴입니다. 지원 되는 값은 아래를 참조 하세요. 지정 하지 않으면 기본값이 `1` 사용 됩니다.

## <a name="returns"></a>반환

그룹에 있는의 고유한 값 수의 예상 값을 반환 합니다 *`Expr`* .

## <a name="example"></a>예제

```kusto
PageViewLog | summarize countries=dcount(country) by continent
```

:::image type="content" source="images/dcount-aggfunction/dcount.png" alt-text="D 개수":::

로 그룹화 된의 고유 값 수를 정확 하 게 가져옵니다 `V` `G` .

```kusto
T | summarize by V, G | summarize count() by G
```

의 고유 값에는 `V` 의 고유 값 수를 곱하여이 계산에는 상당한 양의 내부 메모리가 필요 합니다 `G` .
이로 인해 메모리 오류가 발생 하거나 실행 시간이 커질 수 있습니다. 
`dcount()`빠르고 안정적인 대안을 제공 합니다.

```kusto
T | summarize dcount(B) by G | count
```

## <a name="estimation-accuracy"></a>예측 정확도

`dcount()`집계 함수는 set 카디널리티의 추계 예측을 수행 하는 [Hll (Hyperloglog) 알고리즘](https://en.wikipedia.org/wiki/HyperLogLog)의 변형을 사용 합니다. 알고리즘은 메모리 크기 당 정확성 및 실행 시간을 균형 있게 조정 하는 데 사용할 수 있는 "노브"를 제공 합니다.

|정확도|오류 (%)|항목 수   |
|--------|---------|--------------|
|       0|      1.6|2<sup>12</sup>|
|       1|      0.8|2<sup>14</sup>|
|       2|      0.4|2<sup>16</sup>|
|       3|     0.28|2<sup>17</sup>|
|       4|      0.2|2<sup>18</sup>|

> [!NOTE]
> "항목 수" 열은 HLL 구현에서 1 바이트 카운터의 수입니다.

이 알고리즘에는 설정 카디널리티가 충분 한 경우에는 완전 한 개수 (제로 오류)를 수행 하기 위한 몇 가지 프로 비전이 포함 됩니다.
* 정확도 수준이 이면 `1` 1000 값이 반환 됩니다.
* 정확도 수준이 이면 `2` 8000 값이 반환 됩니다.

바인딩된 오류는 이론적 바운드가 아닌 확률입니다. 값은 오류 분포 (시그마)의 표준 편차 이며, 예측의 99.7%는 3 x 시그마 아래에서의 상대적 오차를 가집니다.

다음 이미지는 지원 되는 모든 정확도 설정에 대해 상대적 예측 오류의 확률 분포 함수를 백분율로 보여 줍니다.

:::image type="content" border="false" source="images/dcount-aggfunction/hll-error-distribution.png" alt-text="hll 오류 배포":::
