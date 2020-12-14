---
title: series_rate_fl ()-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 series_rate_fl () 사용자 정의 함수에 대해 설명 합니다.
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: reference
ms.date: 12/13/2020
ms.openlocfilehash: 6678f17624225895734781f32782ed5b9bd79955
ms.sourcegitcommit: fcaf3056db2481f0e3f4c2324c4ac956a4afef38
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/14/2020
ms.locfileid: "97389175"
---
# <a name="series_rate_fl"></a>series_rate_fl ()


함수는 `series_rate_fl()` 초당 평균 메트릭 증가율을 계산 합니다. 해당 논리는 PromQL [rate ()](https://prometheus.io/docs/prometheus/latest/querying/functions/#rate) 함수를 따릅니다. 이는 수집 () [모니터링 시스템에서 Azure](https://prometheus.io/) 데이터 탐색기에 대 한 시간 일련의 카운터 메트릭에 사용 되며 [series_metric_fl ()](series-metric-fl.md)에서 검색 됩니다.

> [!NOTE]
>`series_rate_fl()` 는 [UDF (사용자 정의 함수)](../query/functions/user-defined-functions.md)입니다. 자세한 내용은 [사용](#usage)을 참조 하세요.

## <a name="syntax"></a>구문

`T | invoke series_rate_fl(`*n_bins*`)`

`T` 는 [series_metric_fl ()](series-metric-fl.md)에서 반환 되는 테이블입니다. 해당 스키마에는가 포함 됩니다 `(timestamp:dynamic, name:string, labels:string, value:dynamic)` .

## <a name="arguments"></a>인수

* *n_bins*: 요율 계산을 위해 추출 된 메트릭 값 사이의 간격을 지정 하는 bin 수입니다. 함수는 현재 샘플과 이전 *n_bins* 사이의 차이를 계산 하 고 해당 타임 스탬프의 차이 (초)로 나눕니다. 이 매개 변수는 선택 사항이 며 기본값은 하나의 bin입니다. 기본 설정은 [irate ()](https://prometheus.io/docs/prometheus/latest/querying/functions/#irate)를 계산 하 고, PromQL 즉각적인 rate 함수를 계산 합니다.

## <a name="usage"></a>사용량

`series_rate_fl()`[호출 연산자](../query/invokeoperator.md)를 사용 하 여 적용 되는 사용자 정의 [테이블 형식 함수](../query/functions/user-defined-functions.md#tabular-function)입니다. 쿼리에 해당 코드를 포함 하거나 데이터베이스에 설치할 수 있습니다. Ad hoc 및 영구 사용의 두 가지 사용 옵션이 있습니다. 예제는 아래 탭을 참조 하세요.

> [!NOTE]
> [series_metric_fl ()](series-metric-fl.md) 은 함수의 일부로 사용 되며 설치 되거나 포함 되어야 합니다.

# <a name="ad-hoc"></a>[임시](#tab/adhoc)

임시 사용의 경우 [let 문을](../query/letstatement.md)사용 하 여 해당 코드를 포함 합니다. 권한이 필요 하지 않습니다.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let series_rate_fl=(tbl:(timestamp:dynamic, name:string, labels:string, value:dynamic), n_bins:int=1)
{
    tbl
    | extend timestampS = array_shift_right(timestamp, n_bins), valueS = array_shift_right(value, n_bins)
    | extend dt = series_subtract(timestamp, timestampS)
    | extend dt = series_divide(dt, 1e7)                              //  converts from ticks to seconds
    | extend dv = series_subtract(value, valueS)
    | extend dv = array_iff(series_greater_equals(dv, 0), dv, value)  //  handles negative difference like PromQL
    | extend rate = series_divide(dv, dt)
    | project timestamp, name, rate, labels
}
;
//
demo_prometheus
| invoke series_metric_fl('TimeStamp', 'Name', 'Labels', 'Val', 'writes', offset=now()-datetime(2020-12-08 00:00))
| invoke series_rate_fl(2)
| render timechart with(series=labels)
```

# <a name="persistent"></a>[Persistent](#tab/persistent)

영구적으로 사용 하려면을 사용 [`.create function`](../management/create-function.md) 합니다. 함수를 만들려면 [데이터베이스 사용자 권한이](../management/access-control/role-based-authorization.md)필요 합니다.

### <a name="one-time-installation"></a>일회성 설치

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
.create-or-alter function with (folder = "Packages\\Series", docstring = "Simulate PromQL rate()")
series_rate_fl(tbl:(timestamp:dynamic, name:string, labels:string, value:dynamic), n_bins:int=1)
{
    tbl
    | extend timestampS = array_shift_right(timestamp, n_bins), valueS = array_shift_right(value, n_bins)
    | extend dt = series_subtract(timestamp, timestampS)
    | extend dt = series_divide(dt, 1e7)                              //  converts from ticks to seconds
    | extend dv = series_subtract(value, valueS)
    | extend dv = array_iff(series_greater_equals(dv, 0), dv, value)  //  handles negative difference like PromQL
    | extend rate = series_divide(dv, dt)
    | project timestamp, name, rate, labels
}
```

### <a name="usage"></a>사용량

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
demo_prometheus
| invoke series_metric_fl('TimeStamp', 'Name', 'Labels', 'Val', 'writes', offset=now()-datetime(2020-12-08 00:00))
| invoke series_rate_fl(2)
| render timechart with(series=labels)
```

---

:::image type="content" source="images/series-rate-fl/all-disks-write-rate-2-bins.png" alt-text="모든 디스크에 대 한 디스크 쓰기 메트릭의 초당 요율을 보여 주는 그래프" border="false":::

## <a name="example"></a>예

다음 예에서는 두 호스트의 주 디스크를 선택 하 고 함수가 이미 설치 된 것으로 가정 합니다. 이 예에서는 첫 번째 매개 변수로 입력 테이블을 지정 하는 대체 직접 호출 구문을 사용 합니다.
    
<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
series_rate_fl(series_metric_fl(demo_prometheus, 'TimeStamp', 'Name', 'Labels', 'Val', 'writes', 'disk:sda1', lookback=2h, offset=now()-datetime(2020-12-08 00:00)), n_bins=10)
| render timechart with(series=labels)
```
    
:::image type="content" source="images/series-rate-fl/main-disks-write-rate-10-bins.png" alt-text="지난 2 시간 동안 10 개의 bin 간격이 있는 주 디스크 쓰기 메트릭의 초당 속도로 표시 되는 그래프입니다." border="false":::
