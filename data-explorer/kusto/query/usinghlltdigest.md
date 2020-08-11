---
title: 중간 집계 결과를 작성 하 & kusto 파티션-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 집계의 중간 결과를 분할 하 고 작성 하는 방법을 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: f56bd1c9f87833f7c1a9d29580a71557fedb894c
ms.sourcegitcommit: ed902a5a781e24e081cd85910ed15cd468a0db1e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/11/2020
ms.locfileid: "88072398"
---
# <a name="using-hll-and-tdigest"></a>hll() 및 tdigest() 사용

지난 7 일 동안 매일 고유한 사용자 수를 계산 하려는 경우를 가정해 보겠습니다. `summarize dcount(user)`지난 7 일간 필터링 된 범위를 사용 하 여 하루에 한 번 실행할 수 있습니다. 이 방법은 비효율적입니다. 계산이 실행 될 때마다 이전 계산과 6 일이 겹칩니다. 또한 각 날짜에 대 한 집계를 계산한 다음 이러한 집계를 결합할 수 있습니다. 이 메서드를 사용 하려면 마지막 6 개 결과를 "명심" 해야 하지만 훨씬 더 효율적입니다.

에 설명 된 대로 쿼리를 분할 하는 것은 및와 같은 간단한 집계에 쉽게 사용할 수 `count()` `sum()` 있습니다. 및 등의 복잡 한 집계에도 유용할 수 있습니다 `dcount()` `percentiles()` . 이 항목에서는 Kusto에서 이러한 계산을 지 원하는 방법을 설명 합니다.

다음 예에서는를 사용 하는 방법을 보여 줍니다 `hll` / `tdigest` . 이러한 명령을 사용 하면 일부 시나리오에서 성능이 크게 향상 됩니다.

> [!NOTE]
> 일부 경우에는 또는 집계 함수에 의해 생성 된 동적 개체가 `hll` `tdigest` 크고 인코딩 정책의 기본 MaxValueSize 속성을 초과할 수 있습니다. 이 경우 개체는 null로 수집 됩니다.
> 예를 들어 정확도 수준 4를 사용 하 여 함수의 출력을 유지 하는 경우 `hll` 개체의 크기는 `hll` 기본 MaxValueSize (1mb)를 초과 합니다.
> 이 문제를 방지 하려면 다음 예제와 같이 열의 인코딩 정책을 수정 합니다.

```kusto
range x from 1 to 1000000 step 1
| summarize hll(x,4)
| project sizeInMb = estimate_data_size(hll_x) / pow(1024,2)
```

|sizeInMb|
|---|
|1.0000524520874|

이러한 종류의 정책을 적용 하기 전에이 개체를 테이블에 수집 null을 수집 합니다.

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

수집 null을 방지 하려면 `bigobject` 다음과 같이를 2mb로 재정의 하는 특수 인코딩 정책 형식을 사용 합니다 `MaxValueSize` .

```kusto
.alter column MyTable.hll_x policy encoding type='bigobject'
```

이제 위의 동일한 테이블에 값을 수집.

```kusto
.set-or-append MyTable <| range x from 1 to 1000000 step 1
| summarize hll(x,4)
```

두 번째 값을 성공적으로 수집. 

```kusto
MyTable
| project isempty(hll_x)
```

|열1|
|---|
|1|
|0|


## <a name="example"></a>예제

`PageViewsHllTDigest` `hll` 각 시간에 표시 되는 페이지 값을 포함 하는 테이블이 있습니다. 이러한 값은에 맞게 표시 하려고 `12h` 합니다. `hll`타임 스탬프를 사용 하 여 집계 함수를 사용 하 여 값을 병합 `hll_merge()` `12h` 합니다. 함수를 사용 `dcount_hll` 하 여 최종 값을 반환 합니다 `dcount` .

```kusto
PageViewsHllTDigest
| summarize merged_hll = hll_merge(hllPage) by bin(Timestamp, 12h)
| project Timestamp , dcount_hll(merged_hll)
```

|타임스탬프|`dcount_hll_merged_hll`|
|---|---|
|2016-05-01 12:00:00.0000000|20056275|
|2016-05-02 00:00:00.0000000|38797623|
|2016-05-02 12:00:00.0000000|39316056|
|2016-05-03 00:00:00.0000000|13685621|

Bin 타임 스탬프 `1d` :

```kusto
PageViewsHllTDigest
| summarize merged_hll = hll_merge(hllPage) by bin(Timestamp, 1d)
| project Timestamp , dcount_hll(merged_hll)
```

|타임스탬프|`dcount_hll_merged_hll`|
|---|---|
|2016-05-01 00:00:00.0000000|20056275|
|2016-05-02 00:00:00.0000000|64135183|
|2016-05-03 00:00:00.0000000|13685621|

`tdigest`각 시간에서을 나타내는 값에 대해 동일한 쿼리를 수행할 수 있습니다 `BytesDelivered` .

```kusto
PageViewsHllTDigest
| summarize merged_tdigests = merge_tdigests(tdigestBytesDel) by bin(Timestamp, 12h)
| project Timestamp , percentile_tdigest(merged_tdigests, 95, typeof(long))
```

|타임스탬프|`percentile_tdigest_merged_tdigests`|
|---|---|
|2016-05-01 12:00:00.0000000|170200|
|2016-05-02 00:00:00.0000000|152975|
|2016-05-02 12:00:00.0000000|181315|
|2016-05-03 00:00:00.0000000|146817|
 
## <a name="example"></a>예제

너무 커서 데이터 집합에 대 한 주기적인 쿼리를 실행 해야 하지만, 일반 쿼리를 실행 하 여 계산 [`percentile()`](percentiles-aggfunction.md) 하거나 대량 데이터 집합을 초과 하는 데이터 집합을 사용 하 여 kusto 제한에 도달 했습니다 [`dcount()`](dcount-aggfunction.md) .

::: zone pivot="azuredataexplorer"

이 문제를 해결 하기 위해 `hll` `tdigest` [`hll()`](hll-aggfunction.md) 필요한 작업이 `dcount` 이거나 [`tdigest()`](tdigest-aggfunction.md) 또는를 사용 하 여 필요한 작업이 백분위 수 인 경우 [`set/append`](../../ingest-data-overview.md) 를 사용 하 여 [`update policy`](../management/updatepolicy.md) 새로 추가 된 데이터를 임시 테이블에 또는 값으로 추가할 수 있습니다. 이 경우 또는의 중간 결과는 `dcount` `tdigest` 다른 데이터 집합에 저장 됩니다 .이 데이터 집합은 대상 보다 작아야 합니다.

::: zone-end

::: zone pivot="azuremonitor"

이 문제를 해결 하기 위해 `hll` `tdigest` [`hll()`](hll-aggfunction.md) 필요한 작업이 일 때를 사용 하 여 새로 추가 된 데이터를 임시 테이블에 또는 값으로 추가할 수 있습니다 `dcount` . 이 경우의 중간 결과는 `dcount` 다른 데이터 집합에 저장 되며,이는 대상 보다 작아야 합니다.

::: zone-end

이러한 값의 최종 결과를 가져와야 하는 경우 쿼리에는 인수를 사용할 수 있습니다 `hll` / `tdigest` [`hll-merge()`](hll-merge-aggfunction.md) / [`tdigest_merge()`](tdigest-merge-aggfunction.md) . 그런 다음 병합 된 값을 가져온 후 [`percentile_tdigest()`](percentile-tdigestfunction.md)  /  [`dcount_hll()`](dcount-hllfunction.md) 이러한 병합 된 값에 대해를 호출 하 여 최종 결과 또는 백분위 수를 가져올 수 있습니다 `dcount` .

데이터를 매일 수집 하는 테이블, 즉 매일 1 분에 표시 되는 페이지의 고유 개수를 계산 하고자 하는 테이블을 가정 합니다 (2016-05-01 18:00:00.0000000).

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

이 쿼리는이 쿼리를 실행할 때마다 모든 값을 집계 합니다 (예: 하루에 여러 번 실행 하려는 경우).

및 `hll` `tdigest` 값 (및 백분위 수의 중간 결과)을 임시 테이블에 저장 하는 경우 `dcount` `PageViewsHllTDigest` 업데이트 정책 또는 set/append 명령을 사용 하면 값만 병합 하 고 `dcount_hll` / `percentile_tdigest` 다음 쿼리를 사용 하 여를 사용할 수 있습니다.

```kusto
PageViewsHllTDigest
| summarize  percentile_tdigest(merge_tdigests(tdigestBytesDel), 90), dcount_hll(hll_merge(hllPage)) by bin(Timestamp, 1d)
```

|타임스탬프|`percentile_tdigest_merge_tdigests_tdigestBytesDel`|`dcount_hll_hll_merge_hllPage`|
|---|---|---|
|2016-05-01 00:00:00.0000000|84224|20056275|
|2016-05-02 00:00:00.0000000|83486|64135183|
|2016-05-03 00:00:00.0000000|72247|13685621|

이 쿼리는 더 작은 테이블에서 실행 되므로 성능이 더 우수 합니다. 이 예에서 첫 번째 쿼리는 215M 개 이상의 레코드를 실행 하는 반면 두 번째 쿼리는 32 개 레코드를 초과 하 여 실행 됩니다.

## <a name="example"></a>예제

보존 쿼리입니다.
각 위키백과 페이지가 표시 되는 시간을 요약 하는 테이블이 있다고 가정 합니다 (샘플 크기는 10M). 각 날짜/시간을 찾으려면 date1 (date1 < date2)에 표시 된 페이지를 기준으로 날짜 1과 날짜 2 모두에서 검토 된 페이지의 백분율을 찾으려고 합니다.
  
간단한 방법은 join 및 요약 연산자를 사용 하는 것입니다.

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

 
위의 쿼리는 18 초 정도 걸렸습니다.

, 및 함수를 사용 하는 경우 [`hll()`](hll-aggfunction.md) [`hll_merge()`](hll-merge-aggfunction.md) [`dcount_hll()`](dcount-hllfunction.md) 해당 쿼리는 ~ 1.3 초 후에 종료 되 고 `hll` 함수에서 쿼리 속도를

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

|day1|출력만|백분율|
|---|---|---|
|2016-05-01 00:00:00.0000000|2016-05-02 00:00:00.0000000|33.2298494510578|
|2016-05-01 00:00:00.0000000|2016-05-03 00:00:00.0000000|16.9773830213667|
|2016-05-02 00:00:00.0000000|2016-05-03 00:00:00.0000000|14.5160020350006|

> [!NOTE] 
> 함수의 오류로 인해 쿼리 결과가 100% 정확 하지 않습니다 `hll` . 오류에 대 한 자세한 내용은을 참조 하십시오 [`dcount()`](dcount-aggfunction.md) .
