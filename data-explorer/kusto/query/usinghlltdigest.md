---
title: 집계의 중간 결과 분할 및 구성 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure Data Explorer에서 집계의 중간 결과를 분할하고 작성하는 것에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: c906e764330efedac051587201b28e5d0fc1ef07
ms.sourcegitcommit: 01eb9aaf1df2ebd5002eb7ea7367a9ef85dc4f5d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81766142"
---
# <a name="partitioning-and-composing-intermediate-results-of-aggregations"></a>집계의 중간 결과 분할 및 구성

지난 7일 동안의 고유 사용자 수를 매일 계산한다고 가정합니다. 한 가지 방법은 지난 7일까지 필터링된 스팬으로 하루에 한 번 실행하는 `summarize dcount(user)` 것입니다. 계산이 실행될 때마다 이전 계산과 6일 겹치기 때문에 비효율적입니다. 또 다른 옵션은 매일 일부 집계를 계산한 다음 효율적인 방식으로 이러한 집계를 결합하는 것입니다. 이 옵션을 사용하려면 마지막 6개의 결과를 "기억"해야 하지만 훨씬 더 효율적입니다.

그런 쿼리를 분할하면 count() 및 sum()과 같은 간단한 집계에서 쉽게 사용할 수 있습니다. 그러나 dcount() 및 백분위수()와 같은 보다 복잡한 집계에 대해서도 수행할 수 있습니다. 이 항목에서는 Kusto가 이러한 계산을 지원하는 방법을 설명합니다.

다음 예제에서는 hll/tdigest를 사용하는 방법을 보여 주며 일부 시나리오에서 이러한 시나리오를 사용하는 것이 다른 방법보다 훨씬 더 성능이 높을 수 있음을 보여 줍니다.

> [!NOTE]
> 경우에 따라 `hll` 또는 `tdigest` 집계 함수에 의해 생성된 동적 개체가 크고 인코딩 정책의 기본 MaxValueSize 속성을 초과할 수 있습니다. 이 경우 개체가 null로 인제됩니다.
예를 들어 `hll` 정확도 수준 4로 함수 출력을 유지하면 hll 개체의 크기가 1MB인 기본 MaxValueSize를 초과합니다.

```kusto
range x from 1 to 1000000 step 1
| summarize hll(x,4)
| project sizeInMb = estimate_data_size(hll_x) / pow(1024,2)
```

|sizeInMb|
|---|
|1.0000524520874|

이러한 종류의 정책을 적용하기 전에 테이블에 이 것을 입력하면 null이 인더스트됩니다.

```kusto
.set-or-append MyTable <| range x from 1 to 1000000 step 1
| summarize hll(x,4)
```

```kusto
MyTable
| project isempty(hll_x)
```

| 열1 |
|---------|
| 1       |

이를 방지하려면 다음과 같이 MaxValueSize를 2MB로 재정의하는 특수 인코딩 정책 형식을 `bigobject`사용합니다.

```kusto
.alter column MyTable.hll_x policy encoding type='bigobject'
```



따라서 위의 동일한 테이블에 값을 이제 인더링하십시오.

```kusto
.set-or-append MyTable <| range x from 1 to 1000000 step 1
| summarize hll(x,4)
```
두 번째 값을 성공적으로 인제스트합니다. 

```kusto
MyTable
| project isempty(hll_x)
```

|열1|
|---|
|1|
|0|


**예제**

매 시간 동안 본 페이지에 대한 hll 값이 있는 테이블인 PageViewsHllTDigest가 있다고 가정합니다. 목표는 이러한 값을 가져오기 위한 것이지만 `12h`에 binned하여 에 binned된 타임스탬프로 hll_merge() `12h`집계 함수를 사용하여 hll 값을 병합하는 것입니다. 그런 다음 함수 dcount_hll 호출하여 최종 dcount 값을 가져옵니다.

```kusto
PageViewsHllTDigest
| summarize merged_hll = hll_merge(hllPage) by bin(Timestamp, 12h)
| project Timestamp , dcount_hll(merged_hll)
```

|타임스탬프|dcount_hll_merged_hll|
|---|---|
|2016-05-01 12:00:00.0000000|20056275|
|2016-05-02 00:00:00.0000000|38797623|
|2016-05-02 12:00:00.0000000|39316056|
|2016-05-03 00:00:00.0000000|13685621|

또는 비닝 타임 스탬프에 대한 `1d`경우에도 :

```kusto
PageViewsHllTDigest
| summarize merged_hll = hll_merge(hllPage) by bin(Timestamp, 1d)
| project Timestamp , dcount_hll(merged_hll)
```

|타임스탬프|dcount_hll_merged_hll|
|---|---|
|2016-05-01 00:00:00.0000000|20056275|
|2016-05-02 00:00:00.0000000|64135183|
|2016-05-03 00:00:00.0000000|13685621|

 같은 일이 각 시간에 BytesDelivered를 나타내는 tdigest의 값을 통해 수행 될 수있다 :

```kusto
PageViewsHllTDigest
| summarize merged_tdigests = merge_tdigests(tdigestBytesDel) by bin(Timestamp, 12h)
| project Timestamp , percentile_tdigest(merged_tdigests, 95, typeof(long))
```

|타임스탬프|percentile_tdigest_merged_tdigests|
|---|---|
|2016-05-01 12:00:00.0000000|170200|
|2016-05-02 00:00:00.0000000|152975|
|2016-05-02 12:00:00.0000000|181315|
|2016-05-03 00:00:00.0000000|146817|
 
**예제**

Kusto 한도는 데이터 집합에 대해 주기적 쿼리를 실행해야 하지만 이러한 빅 데이터 집합을 [`percentile()`](percentiles-aggfunction.md) 계산하거나 [`dcount()`](dcount-aggfunction.md) 계산하기 위해 일반 쿼리를 실행해야 하는 너무 큰 데이터 집합으로 도달합니다.

::: zone pivot="azuredataexplorer"

이 문제를 해결하기 위해 새로 추가된 데이터는 필요한 작업이 dcount이거나 [`hll()`](hll-aggfunction.md) 필요한 작업이 [`tdigest()`](tdigest-aggfunction.md) [`set/append`](../management/data-ingestion/index.md) 백분위수인 경우를 사용하여 hll [`update policy`](../management/updatepolicy.md)또는 tdigest 값으로 임시 테이블에 추가될 수 있습니다.

::: zone-end

::: zone pivot="azuremonitor"

이 문제를 해결하기 위해 새로 추가된 데이터는 필요한 작업이 dcount일 [`hll()`](hll-aggfunction.md) 때를 사용하여 hll 또는 tdigest 값으로 임시 테이블에 추가될 수 있으며, 이 경우 dcount의 중간 결과가 다른 데이터 집합에 저장되어 대상 큰 데이터보다 작아야 합니다.

::: zone-end

이러한 값의 최종 결과를 얻어야 하는 경우 쿼리는 hll/tdigest 병합을 사용할 [`hll-merge()`](hll-merge-aggfunction.md) / [`tdigest_merge()`](tdigest-merge-aggfunction.md) [`percentile_tdigest()`](percentile-tdigestfunction.md)  /  [`dcount_hll()`](dcount-hllfunction.md) 수 있습니다.

데이터가 매일 수집되는 테이블인 PageViews가 있다고 가정하면, 날짜 = 날짜(2016-05-01 18:00:00.00000)보다 나중에 minuite당 조회된 페이지의 고유한 수를 계산하려는 경우 매일 계산합니다.

다음 쿼리를 실행합니다.

```kusto
PageViews   
| where Timestamp > datetime(2016-05-01 18:00:00.0000000)
| summarize percentile(BytesDelivered, 90), dcount(Page,2) by bin(Timestamp, 1d)
```

|타임스탬프|percentile_BytesDelivered_90|dcount_Page|
|---|---|---|
|2016-05-01 00:00:00.0000000|83634|20056275|
|2016-05-02 00:00:00.0000000|82770|64135183|
|2016-05-03 00:00:00.0000000|72920|13685621|

이 쿼리는 이 쿼리를 실행할 때마다 모든 값을 집계합니다(예: 하루에 여러 번 실행하려는 경우).

hll 및 tdigest 값(dcount 및 백분위수의 중간 결과)을 임시 테이블인 PageViewsHllTDigest에 업데이트 정책 또는 세트/추가 명령을 사용하는 경우 다음 쿼리를 사용하여 값만 병합한 다음 dcount_hll/percentile_tdigest 사용할 수 있습니다.

```kusto
PageViewsHllTDigest
| summarize  percentile_tdigest(merge_tdigests(tdigestBytesDel), 90), dcount_hll(hll_merge(hllPage)) by bin(Timestamp, 1d)
```

|타임스탬프|percentile_tdigest_merge_tdigests_tdigestBytesDel|dcount_hll_hll_merge_hllPage|
|---|---|---|
|2016-05-01 00:00:00.0000000|84224|20056275|
|2016-05-02 00:00:00.0000000|83486|64135183|
|2016-05-03 00:00:00.0000000|72247|13685621|

이 성능이 더 우수하고 쿼리가 더 작은 테이블에서 실행되어야 합니다(이 예제에서는 첫 번째 쿼리가 ~215M 레코드를 통해 실행되는 반면 두 번째 쿼리는 32개 레코드에서만 실행됩니다).

**예제**

보존 쿼리입니다.
각 위키백과 페이지를 볼 때 요약하는 테이블이 있다고 가정하면(샘플 크기는 10M), 각 date1 date1에 대해 날짜1과 date2에서 검토된 페이지의 백분율을 date1(date1 < date2)에 대해 모두 검토한 페이지 비율을 찾으려는 경우(date1 < date2).
  
간단한 방법은 조인을 사용하고 연산자 요약 :

```kusto
// Get the total pages viewed each day
let totalPagesPerDay = PageViewsSample
| summarize by Page, Day = startofday(Timestamp)
| summarize count() by Day;
// Join the table to itself to get a grid where 
// each row shows foreach page1, in which two dates
// it was viewed.
// Then count the pages between each two dates to
// get how many pages were viewed between date1 and date2.
PageViewsSample
| summarize by Page, Day1 = startofday(Timestamp)
| join kind = inner
(
    PageViewsSample
    | summarize by Page, Day2 = startofday(Timestamp)
)
on Page
| where Day2 > Day1
| summarize count() by Day1, Day2
| join kind = inner
    totalPagesPerDay
on $left.Day1 == $right.Day
| project Day1, Day2, Percentage = count_*100.0/count_1
```

|Day1|Day2|백분율|
|---|---|---|
|2016-05-01 00:00:00.0000000|2016-05-02 00:00:00.0000000|34.0645725975255|
|2016-05-01 00:00:00.0000000|2016-05-03 00:00:00.0000000|16.618368960101|
|2016-05-02 00:00:00.0000000|2016-05-03 00:00:00.0000000|14.6291376489636|

 
위의 쿼리는 ~ 18 초가 걸렸습니다.

의 [`hll()`](hll-aggfunction.md)함수를 사용하는 [`hll_merge()`](hll-merge-aggfunction.md) 경우 [`dcount_hll()`](dcount-hllfunction.md), 등가 쿼리는 ~ 1.3 초 후에 끝나고 hll 함수가 위의 쿼리 속도를 ~ 14 배 로 가속화한다는 것을 보여줍니다.

```kusto
let Stats=PageViewsSample | summarize pagehll=hll(Page, 2) by day=startofday(Timestamp); // saving the hll values (intermediate results of the dcount values)
let day0=toscalar(Stats | summarize min(day)); // finding the min date over all dates.
let dayn=toscalar(Stats | summarize max(day)); // finding the max date over all dates.
let daycount=tolong((dayn-day0)/1d); // finding the range between max and min
Stats
| project idx=tolong((day-day0)/1d), day, pagehll
| mv-expand pidx=range(0, daycount) to typeof(long)
// Extend the column to get the dcount value from hll'ed values for each date (same as totalPagesPerDay from the above query)
| extend key1=iff(idx < pidx, idx, pidx), key2=iff(idx < pidx, pidx, idx), pages=dcount_hll(pagehll)
// For each two dates, merge the hll'ed values to get the total dcount over each two dates, 
// This helps to get the pages viewed in both date1 and date2 (see the description below about the intersection_size)
| summarize (day1, pages1)=arg_min(day, pages), (day2, pages2)=arg_max(day, pages), union_size=dcount_hll(hll_merge(pagehll)) by key1, key2
| where day2 > day1
// To get pages viewed in date1 and also date2, look at the merged dcount of date1 and date2, subtract it from pages of date1 + pages on date2.
| project pages1, day1,day2, intersection_size=(pages1 + pages2 - union_size)
| project day1, day2, Percentage = intersection_size*100.0 / pages1
```

|1일차|2일차|백분율|
|---|---|---|
|2016-05-01 00:00:00.0000000|2016-05-02 00:00:00.0000000|33.2298494510578|
|2016-05-01 00:00:00.0000000|2016-05-03 00:00:00.0000000|16.9773830213667|
|2016-05-02 00:00:00.0000000|2016-05-03 00:00:00.0000000|14.5160020350006|

> [!NOTE] 
> hll 함수의 오류로 인해 쿼리 결과가 100% 정확하지 않습니다. (오류에 대한 자세한 내용은 참조). [`dcount()`](dcount-aggfunction.md)
