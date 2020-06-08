---
title: 백분위 수 (), 백분위 수 ()-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 백분위 수 (), 백분위 수 ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/30/2020
ms.openlocfilehash: dfb957189eb0d9be552cf12b32ef57452a375c51
ms.sourcegitcommit: 188f89553b9d0230a8e7152fa1fce56c09ebb6d6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/08/2020
ms.locfileid: "84512557"
---
# <a name="percentile-percentiles"></a>백분위 수 (), 백분위 수 ()

에서 정의한 모집단의 지정 된 [가장 가까운 순위 백분위](#nearest-rank-percentile) 수에 대 한 예상 값을 반환 합니다 `*Expr*` .
정확도는 백분위수 지역의 인구 밀도에 따라 달라집니다. 이 함수는 [요약](summarizeoperator.md) 내의 집계 컨텍스트에서만 사용할 수 있습니다.

* `percentiles()`는와 유사 `percentile()` 하지만 각 백분위 수를 개별적으로 계산 하는 것 보다 더 빠른 백분위 수 값을 계산 합니다.
* `percentilesw()`는와 유사 `percentilew()` 하지만 각 백분위 수를 개별적으로 계산 하는 것 보다 더 빠른 가중치가 적용 된 백분위 수 값의 수를 계산 합니다.
* `percentilew()`가중치가 적용 되는 `percentilesw()` 백분위 수을 계산할 수 있습니다. 가중치가 적용 된 백분위 수는 입력에서 각 값을 반복 된 시간으로 처리 하 여 "가중치가 적용 된" 방식으로 지정 된 백분위 수를 계산 합니다 `weight` .

**구문**

`percentile(` *식* `,` *백분위* 수 요약`)`

`percentiles(` *식* 요약 `,` *Percentile1* [ `,` *Percentile2*]`)`

`percentiles_array(` *식* 요약 `,` *Percentile1* [ `,` *Percentile2*]`)`

`percentiles_array(` *Expr* `,` *동적 배열* 요약`)`

`percentilew(` *Expr* `,` *WeightExpr* `,` *백분위* 수 요약`)`

`percentilesw(` *식* 요약 `,` *WeightExpr* `,` *Percentile1* [ `,` *Percentile2*]`)`

`percentilesw_array(` *식* 요약 `,` *WeightExpr* `,` *Percentile1* [ `,` *Percentile2*]`)`

`percentilesw_array(` *Expr* `,` *WeightExpr* `,` *동적 배열* 요약`)`

**인수**

* `*Expr*`: 집계 계산에 사용 되는 식입니다.
* `*WeightExpr*`: 집계 계산에 대 한 값의 가중치로 사용 되는 식입니다.
* `*Percentile*`: 백분위 수를 지정 하는 double 상수입니다.
* `*Dynamic array*`: 정수 또는 부동 소수점 숫자의 동적 배열에 있는 백분위 수의 목록입니다.

**반환**

그룹의 지정 된 백분위 수에 대 한 예상 값을 반환 합니다 `*Expr*` . 

**예**

`Duration`샘플 집합의 95% 보다 크고 샘플 집합의 5% 보다 작은의 값입니다.

```kusto
CallDetailRecords | summarize percentile(Duration, 95) by continent
```

5, 50 (중앙값) 및 95을 동시에 계산 합니다.

```kusto
CallDetailRecords 
| summarize percentiles(Duration, 5, 50, 95) by continent
```

:::image type="content" source="images/percentiles-aggfunction/percentiles.png" alt-text="백분위 수":::

결과는 유럽에서 5%의 호출이 11.55 s 보다 짧습니다. 50%의 호출이 3 분, 18.46 초 미만, 95%는 40 분 48 초 보다 짧습니다.

```kusto
CallDetailRecords 
| summarize percentiles(Duration, 5, 50, 95), avg(Duration)
```

## <a name="weighted-percentiles"></a>가중치가 적용된 백분위 수입니다.

작업을 완료 하는 데 걸리는 시간 (기간)을 반복적으로 측정 한다고 가정 합니다. 측정의 모든 값을 기록 하는 대신 각 기간의 값을 100 밀리초로 반올림 하 고 반올림 된 값이 표시 되는 횟수 (BucketSize)를 기록 합니다.

`summarize percentilesw(Duration, BucketSize, ...)`지정 된 백분위 수를 "가중치가 적용 된" 방식으로 계산 하는 데 사용 합니다. BucketSize 시간이 입력에서 반복 되는 것 처럼 지속 되는 것 처럼 각 기간 값을 실제로 처리 합니다.

**예제**

고객의 대기 시간 값은 밀리초로 설정 `{ 1, 1, 2, 2, 2, 5, 7, 7, 12, 12, 15, 15, 15, 18, 21, 22, 26, 35 }` 됩니다.

대역폭과 저장소를 줄이려면 다음 버킷에 대 한 사전 집계를 수행 `{ 10, 20, 30, 40, 50, 100 }` 합니다. 각 버킷의 이벤트 수를 계산 하 여 다음 테이블을 생성 합니다.

:::image type="content" source="images/percentiles-aggfunction/percentilesw-table.png" alt-text="Percentilesw 테이블":::

테이블에 다음이 표시 됩니다.
 * 10-ms 버킷의 8 개 이벤트 (하위 집합에 해당 `{ 1, 1, 2, 2, 2, 5, 7, 7 }` )
 * 20 밀리초 버킷에 있는 6 개 이벤트 (하위 집합에 해당 `{ 12, 12, 15, 15, 15, 18 }` )
 * 30 밀리초 버킷의 세 가지 이벤트 (하위 집합에 해당 `{ 21, 22, 26 }` )
 * 40-ms 버킷의 이벤트 하나 (하위 집합에 해당 `{ 35 }` )

이 시점에서 원래 데이터를 더 이상 사용할 수 없습니다. 각 버킷의 이벤트 수만 이 데이터에서 백분위 수를 계산 하려면 함수를 사용 `percentilesw()` 합니다.
예를 들어 50, 75 및 99.9 백분위 수의 경우 다음 쿼리를 사용 합니다.

```kusto
datatable (ReqCount:long, LatencyBucket:long) 
[ 
    8, 10, 
    6, 20, 
    3, 30, 
    1, 40 
]
| summarize percentilesw(LatencyBucket, ReqCount, 50, 75, 99.9) 
```

위의 쿼리 결과는 다음과 같습니다.

:::image type="content" source="images/percentiles-aggfunction/percentilesw-result.png" alt-text="Percentilesw 결과" border="false":::


위의 쿼리는 `percentiles(LatencyBucket, 50, 75, 99.9)` 데이터가 다음 형식으로 확장 된 경우 함수에 해당 합니다.

:::image type="content" source="images/percentiles-aggfunction/percentilesw-rawtable.png" alt-text="Percentilesw raw 테이블":::

## <a name="getting-multiple-percentiles-in-an-array"></a>배열에서 여러 백분위 수 가져오기

여러 열이 아닌 단일 동적 열에서 여러 백분위 수를 배열로 가져올 수 있습니다.

```kusto
CallDetailRecords 
| summarize percentiles_array(Duration, 5, 25, 50, 75, 95), avg(Duration)
```

:::image type="content" source="images/percentiles-aggfunction/percentiles-array-result.png" alt-text="백분위 수 배열 결과":::

마찬가지로 가중치가 적용 된 백분위 수는를 사용 하 여 동적 배열로 반환 될 수 있습니다 `percentilesw_array` .

및에 대 한 백분위 수은 `percentiles_array` `percentilesw_array` 정수 또는 부동 소수점 숫자의 동적 배열에 지정할 수 있습니다. 배열은 상수 여야 하지만 리터럴이 될 필요는 없습니다.

```kusto
CallDetailRecords 
| summarize percentiles_array(Duration, dynamic([5, 25, 50, 75, 95])), avg(Duration)
```

```kusto
CallDetailRecords 
| summarize percentiles_array(Duration, range(0, 100, 5)), avg(Duration)
```

## <a name="nearest-rank-percentile"></a>가장 가까운 순위 백분위 수

최소에서 가장 큰 값으로 정렬 된 정렬 된 값 목록의 *P*-th 백분위 수 (0 < *P* <= 100)는 목록에서 가장 작은 값입니다. 데이터의 *p* 비율은 *p*-th 백분위 수 값 ([백분위 수의 위키백과 문서](https://en.wikipedia.org/wiki/Percentile#The_Nearest_Rank_method)) 보다 작거나 같습니다.

*0*번째 백분위 수를 모집단의 가장 작은 멤버로 정의 합니다.

>[!NOTE]
> 계산의 특성을 고려 하 여 실제 반환 된 값은 모집단의 멤버가 아닐 수 있습니다.
> 가장 근사한 순위 정의는 *P*= 50이 [중앙값의 interpolative 정의](https://en.wikipedia.org/wiki/Median)를 따르지 않음을 의미 합니다. 특정 응용 프로그램에 대해 이러한 불일치의 중요성을 평가할 때 채우기 크기와 [예측 오류](#estimation-error-in-percentiles) 를 고려해 야 합니다.

## <a name="estimation-error-in-percentiles"></a>백분위수 추정 오류

백분위 수 집계는 [T-Digest](https://github.com/tdunning/t-digest/blob/master/docs/t-digest-paper/histo.pdf)를 사용하여 근사값을 제공합니다.

>[!NOTE]
> * 추정 오류의 범위는 요청한 백분위수의 값에 따라 달라집니다. 최상의 정확도는 [0.100] 눈금의 양쪽 끝에 있습니다. 백분위 수 0 및 100은 분포의 정확한 최소값과 최대값입니다. 정확도는 눈금의 중앙으로 갈수록 서서히 감소합니다. 가장 낮은 중앙값 이며 1%가 있습니다.
> * 오류 범위는 값이 아닌 순위에서 관찰됩니다. 백분위 수 (X, 50)에서 Xm 값을 반환 했다고 가정 합니다. 예측은 최소 49%와 X 값의 51%가 Xm 보다 작거나 같다고 보장 합니다. Xm과 X의 실제 중앙값 값의 차이에 대 한 이론적 제한은 없습니다.
> * 예측은 정확한 값을 반환 하지만 경우에 따라 신뢰할 수 있는 조건을 정의할 수 없습니다.
