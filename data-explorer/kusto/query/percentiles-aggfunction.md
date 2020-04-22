---
title: 백분위수(), 백분위수() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기에서 백분위수(), 백분위수()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/30/2020
ms.openlocfilehash: ecbb56305cfc43033ca172071f48b25768de6d2f
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663652"
---
# <a name="percentile-percentiles"></a>백분위수(), 백분위수()

*Expr에*의해 정의된 채우기의 지정된 [가장 가까운 순위 백분위수에](#nearest-rank-percentile) 대한 추정치를 반환합니다. 정확도는 백분위수 지역의 인구 밀도에 따라 달라집니다. 이 함수는 요약 내부 집계의 컨텍스트에서만 사용할 수 [있습니다.](summarizeoperator.md)

* `percentiles()`와 `percentile()`같지만 여러 백분위수 값을 계산합니다(각 백분위수를 개별적으로 계산하는 것보다 빠름).
* `percentilesw()`는 `percentilew()`같지만 가중치가 있는 백분위수 값(각 백분위수를 개별적으로 계산하는 것보다 빠름)을 계산합니다.
* `percentilew()`가중 `percentilesw()` 백분위수 계산을 허용합니다. 가중 백분위수는 주어진 백분위수("가중치" 방식으로 계산)를 계산하여 `Weight` 각 값을 입력에서 반복된 시간처럼 처리합니다.

**구문**

`percentile(` *Expr* `,` *백분위수* 요약`)`

요약 `percentiles(` *Expr* `,` *백분위수1* [`,` *백분위수2]*`)`

요약 `percentiles_array(` *Expr* `,` *백분위수1* [`,` *백분위수2]*`)`

`percentiles_array(` *Expr* `,` *동적 배열* 요약`)`

`percentilew(`요약 *익스프런스* `,` *웨이트엑스프* `,` *백분위수*`)`

`percentilesw(`요약 *Expr* `,` *WeightExpr* `,` *백분위수1* [`,` *백분위수2]*`)`

`percentilesw_array(`요약 *Expr* `,` *WeightExpr* `,` *백분위수1* [`,` *백분위수2]*`)`

`percentilesw_array(` *요약 Expr* `,` *WeightExpr* `,` *동적 배열*`)`

**인수**

* *Expr*: 집계 계산에 사용되는 표현식입니다.
* *WeightExpr*: 집계 계산을 위한 값의 가중치로 사용되는 표현식입니다.
* *백분위수는* 백분위수를 지정하는 이중 상수입니다.
* *동적 배열*: 정수 또는 부동 점 번호의 동적 배열의 백분위수 목록입니다.

**반환**

그룹에서 지정된 백분위수의 *Expr에* 대한 예상을 반환합니다. 

**예**

`Duration` 이 값은 샘플 집합의 95% 보다 크고 샘플 집합의 5% 미만입니다.

```kusto
CallDetailRecords | summarize percentile(Duration, 95) by continent
```

동시에 5, 50 (중앙값) 및 95를 계산합니다.

```kusto
CallDetailRecords 
| summarize percentiles(Duration, 5, 50, 95) by continent
```

:::image type="content" source="images/aggregations/percentiles.png" alt-text="백분위수":::

그 결과 유럽에서는 통화의 5%가 11.55초보다 짧고, 통화의 50%가 3분, 18.46초, 95%가 40분 48초보다 짧다는 것을 보여줍니다.

여러 통계를 계산합니다.

```kusto
CallDetailRecords 
| summarize percentiles(Duration, 5, 50, 95), avg(Duration)
```

## <a name="weighted-percentiles"></a>가중치가 적용된 백분위 수입니다.

작업을 반복하는 데 걸리는 시간(기간)을 측정한다고 가정합니다. 측정값의 모든 값을 기록하는 대신 100msec으로 반올림된 기간의 각 값과 반올림된 값이 나타난 횟수(BucketSize)를 기록하여 이를 압축합니다.

실제로 이러한 `summarize percentilesw(Duration, BucketSize, ...)` 레코드를 구체화할 필요 없이 입력에서 BucketSize 시간을 반복한 것처럼 기간의 각 값을 처리하는 "가중치" 방식으로 지정된 백분위수를 계산하는 데 사용할 수 있습니다.

예: 고객이 밀리초 단위로 대기 시간 값 `{ 1, 1, 2, 2, 2, 5, 7, 7, 12, 12, 15, 15, 15, 18, 21, 22, 26, 35 }`집합을 가지고 있습니다.

대역폭 및 저장소를 줄이려면 다음 버킷에 `{ 10, 20, 30, 40, 50, 100 }`대한 사전 집계를 수행하고 각 버킷의 이벤트 수를 계산하여 다음 Kusto 테이블을 제공합니다.

:::image type="content" source="images/aggregations/percentilesw-table.png" alt-text="백분위수 테이블":::

다음과 같이 읽을 수 있습니다.
 * 10ms 버킷의 8개 이벤트(하위 `{ 1, 1, 2, 2, 2, 5, 7, 7 }`집합에 해당)
 * 20ms 버킷의 6개 이벤트(하위 `{ 12, 12, 15, 15, 15, 18 }`집합에 해당)
 * 30ms 버킷의 이벤트 3개(하위 `{ 21, 22, 26 }`집합에 해당)
 * 40ms 버킷의 이벤트 1개(하위 `{ 35 }`집합에 해당)

이 시점에서 원래 데이터를 더 이상 사용할 수 없으며 각 버킷의 이벤트 수만 사용할 수 있습니다. 이 데이터에서 백분위수 계산하려면 `percentilesw()` 함수를 사용합니다. 예를 들어 50, 75 및 99.9 백분위수의 경우 다음 쿼리를 사용합니다. 

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

위의 쿼리에 대한 결과는 다음과 같은 것입니다.

:::image type="content" source="images/aggregations/percentilesw-result.PNG" alt-text="백분위수 결과":::

위의 쿼리는 데이터가 다음 양식에 소비된 경우 함수에 `percentiles(LatencyBucket, 50, 75, 99.9)` 해당합니다.

:::image type="content" source="images/aggregations/percentilesw-rawtable.png" alt-text="백분위수 원시 테이블":::

## <a name="getting-multiple-percentiles-in-an-array"></a>배열에서 여러 백분위수 얻기

여러 백분위수는 여러 열 대신 단일 동적 열에서 배열로 가져올 수 있습니다. 

```kusto
CallDetailRecords 
| summarize percentiles_array(Duration, 5, 25, 50, 75, 95), avg(Duration)
```

:::image type="content" source="images/aggregations/percentiles-array-result.png" alt-text="백분위수 배열 결과":::

마찬가지로 가중 백분위수는 다음을 사용하여 동적 배열로 반환할 수 있습니다.`percentilesw_array`

정수 또는 `percentiles_array` `percentilesw_array` 부동 점 번호의 동적 배열에 지정할 수 있습니다. 배열은 일정해야 하지만 리터럴일 필요는 없습니다.

```kusto
CallDetailRecords 
| summarize percentiles_array(Duration, dynamic([5, 25, 50, 75, 95])), avg(Duration)
```

```kusto
CallDetailRecords 
| summarize percentiles_array(Duration, range(0, 100, 5)), avg(Duration)
```

## <a name="nearest-rank-percentile"></a>가장 가까운 순위 백분위수
*P*-th 백분위수(0< *P* <= 100)의 정렬된 값 목록(최소에서 최대값으로 정렬)은 데이터의 *P* 퍼센트가 해당 값보다 적거나 같도록 목록에서 가장 작은 값입니다(백분위수에[대한 위키백과 문서에서)](https://en.wikipedia.org/wiki/Percentile#The_Nearest_Rank_method)

*0-th*백분위수를 인구의 가장 작은 멤버로 정의합니다.

>[!NOTE]
> 계산의 대략적인 특성을 감안할 때 실제 반환된 값은 인구의 구성원이 아닐 수 있습니다.
> 가장 가까운 순위 정의는 *P*=50이 [중앙값의 보간 정의를](https://en.wikipedia.org/wiki/Median)따르지 않음을 의미합니다. 특정 응용 프로그램에 대한 이 불일치의 중요성을 평가할 때는 채우기의 크기와 [추정 오차를](#estimation-error-in-percentiles) 고려해야 합니다.

## <a name="estimation-error-in-percentiles"></a>백분위수 추정 오류

백분위 수 집계는 [T-Digest](https://github.com/tdunning/t-digest/blob/master/docs/t-digest-paper/histo.pdf)를 사용하여 근사값을 제공합니다. 

>[!NOTE]
> * 추정 오류의 범위는 요청한 백분위수의 값에 따라 달라집니다. 가장 좋은 정확도는 [0..100] 눈금의 끝에 있습니다. 백분위수 0과 100은 분포의 정확한 최소 값과 최대값입니다. 정확도는 눈금의 중앙으로 갈수록 서서히 감소합니다. 중앙값에서 최악이며 1%로 제한됩니다. 
> * 오류 범위는 값이 아닌 순위에서 관찰됩니다. 백분위수(X, 50)가 Xm 값을 반환한다고 가정합니다. 추정은 X 값의 최소 49%와 최대 51%가 Xm보다 적거나 동일하다는 것을 보장합니다. Xm과 X의 실제 중앙값 간의 차이에는 이론적 제한이 없습니다.
> * 추정은 때때로 정확한 값을 초래할 수 있지만 언제 그럴지 정의할 수 있는 신뢰할 수 있는 조건이 없습니다.
