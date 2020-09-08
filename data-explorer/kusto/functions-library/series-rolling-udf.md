---
title: series_rolling_udf ()-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 series_rolling_udf () 사용자 정의 함수에 대해 설명 합니다.
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: reference
ms.date: 09/08/2020
ms.openlocfilehash: bc4431e00b3947aa80404e4ba131dda7e813800e
ms.sourcegitcommit: f689547c0f77b1b8bfa50a19a4518cbbc6d408e5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/08/2020
ms.locfileid: "89557952"
---
# <a name="series_rolling_udf"></a>series_rolling_udf ()


함수는 `series_rolling_udf()` 계열에 롤링 집계를 적용 합니다. 여러 계열 (동적 숫자 배열)을 포함 하는 테이블을 사용 하 여 각 계열에 대해 롤링 집계 함수를 적용 합니다.

> [!NOTE]
> series_rolling_udf ():
>* 인라인 Python을 포함 하며 클러스터에서 [python () 플러그 인을 사용 하도록 설정](../query/pythonplugin.md#enable-the-plugin) 해야 합니다.
>* 는 [UDF (사용자 정의 함수)](../query/functions/user-defined-functions.md)입니다. 자세한 내용은 [사용](#usage)을 참조 하세요.

## <a name="syntax"></a>구문

`T | invoke series_rolling_udf(`*y_series* `,` *y_rolling_series* `,` *n* `,` *aggr* `,` *aggr_params* `,` *센터*`)`

## <a name="arguments"></a>인수

* *y_series*: 일치 하는 계열을 포함 하는 열 (입력 테이블의) 이름입니다.
* *y_rolling_series*: 롤링 집계 계열을 저장할 열의 이름입니다.
* *n*: 롤링 창의 너비입니다.
* *aggr*: 사용할 집계 함수의 이름입니다. [집계 함수](#aggregation-functions)를 참조 하세요.
* *aggr_params*: 집계 함수에 대 한 선택적 매개 변수입니다.
* *center*: 롤링 창이 다음 옵션 중 하나 인지 여부를 나타내는 선택적 부울 값입니다.
    * 현재 지점 앞과 뒤에 대칭으로 적용 되거나 
    * 현재 지점에서 뒤로 적용 됩니다. <br>
    기본적으로 스트리밍 데이터에 대 한 계산에는 *center* 가 false입니다.

## <a name="aggregation-functions"></a>집계 함수

이 함수는 계열의 스칼라를 계산 하는 [numpy](https://numpy.org/) 또는 scipy의 모든 집계 함수를 지원 합니다 [.](https://docs.scipy.org/doc/scipy/reference/stats.html#module-scipy.stats) 다음 목록은 완전 하지 않습니다.

* [`sum`](https://numpy.org/doc/stable/reference/generated/numpy.sum.html#numpy.sum) 
* [`mean`](https://numpy.org/doc/stable/reference/generated/numpy.mean.html?highlight=mean#numpy.mean), [`min`](https://numpy.org/doc/stable/reference/generated/numpy.amin.html#numpy.amin)
* [`max`](https://numpy.org/doc/stable/reference/generated/numpy.amax.html)
* [`ptp (max-min)`](https://numpy.org/doc/stable/reference/generated/numpy.ptp.html)
* [`percentile`](https://numpy.org/doc/stable/reference/generated/numpy.percentile.html)
* [`median`](https://numpy.org/doc/stable/reference/generated/numpy.median.html)
* [`std`](https://numpy.org/doc/stable/reference/generated/numpy.std.html)
* [`var`](https://numpy.org/doc/stable/reference/generated/numpy.var.html)
* [`gmean` (기 하 평균)](https://docs.scipy.org/doc/scipy/reference/generated/scipy.stats.gmean.html)
* [`hmean` (조화 평균)](https://docs.scipy.org/doc/scipy/reference/generated/scipy.stats.hmean.html)
* [`mode` (가장 일반적인 값)](https://docs.scipy.org/doc/scipy/reference/generated/scipy.stats.mode.html)
* [`moment` (n<sup>번째</sup> 순간)](https://docs.scipy.org/doc/scipy/reference/generated/scipy.stats.moment.html)
* [`tmean` (트리밍된 평균)](https://docs.scipy.org/doc/scipy/reference/generated/scipy.stats.tmean.html)
* [`tmin`](https://docs.scipy.org/doc/scipy/reference/generated/scipy.stats.tmin.html), [`tmax`](https://docs.scipy.org/doc/scipy/reference/generated/scipy.stats.tmax.html)
* [tstd](https://docs.scipy.org/doc/scipy/reference/generated/scipy.stats.tstd.html)
* [`iqr` (변 위치 범위 간)](https://docs.scipy.org/doc/scipy/reference/generated/scipy.stats.iqr.html) 

## <a name="usage"></a>사용량

* `series_rolling_udf()` 는 사용자 정의 함수입니다. 쿼리에 해당 코드를 포함 하거나 데이터베이스에 설치할 수 있습니다.
    * 임시 사용의 경우 [let 문을](../query/letstatement.md)사용 하 여 해당 코드를 포함 합니다. 권한이 필요 하지 않습니다.
    * 되풀이 사용의 경우 [create 함수](../management/create-function.md)를 사용 하 여 함수를 유지 합니다. <br>
        함수를 만들려면 [데이터베이스 사용자 권한이](../management/access-control/role-based-authorization.md)필요 합니다.
* `series_rolling_udf()`는 [invoke 연산자](../query/invokeoperator.md)를 사용 하 여 적용 되는 [테이블 형식 함수](../query/functions/user-defined-functions.md#tabular-function)입니다.

# <a name="ad-hoc-usage"></a>[임시 사용](#tab/adhoc)

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let series_rolling_udf = (tbl:(*), y_series:string, y_rolling_series:string, n:int, aggr:string, aggr_params:dynamic=dynamic([null]), center:bool=true)
{
    let kwargs = pack('y_series', y_series, 'y_rolling_series', y_rolling_series, 'n', n, 'aggr', aggr, 'aggr_params', aggr_params, 'center', center);
    let code =
        '\n'
        'y_series = kargs["y_series"]\n'
        'y_rolling_series = kargs["y_rolling_series"]\n'
        'n = kargs["n"]\n'
        'aggr = kargs["aggr"]\n'
        'aggr_params = kargs["aggr_params"]\n'
        'center = kargs["center"]\n'
        'result = df\n'
        'in_s = df[y_series]\n'
        'func = getattr(np, aggr, None)\n'
        'if not func:\n'
        '    import scipy.stats\n'
        '    func = getattr(scipy.stats, aggr)\n'
        'if func:\n'
        '    result[y_rolling_series] = list(pd.Series(in_s[i]).rolling(n, center=center, min_periods=1).apply(func, args=aggr_params).values for i in range(len(in_s)))\n'
        '\n';
    tbl
    | evaluate python(typeof(*), code, kwargs)
}
;
//
//  Calculate rolling median of 9 elements
//
demo_make_series1
| make-series num=count() on TimeStamp step 1h by OsVer
| extend rolling_med = dynamic(null)
| invoke series_rolling_udf('num', 'rolling_med', 9, 'median')
| render timechart
```

# <a name="persistent-usage"></a>[영구 사용](#tab/persistent)

* **일회성 설치**
<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
.create-or-alter function with (folder = "Packages\\Series", docstring = "Rolling window functions on a series")
series_rolling_udf(tbl:(*), y_series:string, y_rolling_series:string, n:int, aggr:string, aggr_params:dynamic, center:bool=true)
{
    let kwargs = pack('y_series', y_series, 'y_rolling_series', y_rolling_series, 'n', n, 'aggr', aggr, 'aggr_params', aggr_params, 'center', center);
    let code =
        '\n'
        'y_series = kargs["y_series"]\n'
        'y_rolling_series = kargs["y_rolling_series"]\n'
        'n = kargs["n"]\n'
        'aggr = kargs["aggr"]\n'
        'aggr_params = kargs["aggr_params"]\n'
        'center = kargs["center"]\n'
        'result = df\n'
        'in_s = df[y_series]\n'
        'func = getattr(np, aggr, None)\n'
        'if not func:\n'
        '    import scipy.stats\n'
        '    func = getattr(scipy.stats, aggr)\n'
        'if func:\n'
        '    result[y_rolling_series] = list(pd.Series(in_s[i]).rolling(n, center=center, min_periods=1).apply(func, args=aggr_params).values for i in range(len(in_s)))\n'
        '\n';
    tbl
    | evaluate python(typeof(*), code, kwargs)
}
```

* **사용 현황**
<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
//
//  Calculate rolling median of 9 elements
//
demo_make_series1
| make-series num=count() on TimeStamp step 1h by OsVer
| extend rolling_med = dynamic(null)
| invoke series_rolling_udf('num', 'rolling_med', 9, 'median', dynamic([null]))
| render timechart
```

---

:::image type="content" source="images/series-rolling-udf/rolling-median-9.png" alt-text="9 개 요소의 롤링 중앙값을 보여 주는 그래프" border="false":::

## <a name="additional-examples"></a>추가 예

다음 예에서는 함수가 이미 설치 되어 있다고 가정 합니다.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
//
//  Calculate rolling min, max & 75th percentile of 15 elements
//
demo_make_series1
| make-series num=count() on TimeStamp step 1h by OsVer
| extend rolling_min = dynamic(null), rolling_max = dynamic(null), rolling_pct = dynamic(null)
| invoke series_rolling_udf('num', 'rolling_min', 15, 'min', dynamic([null]))
| invoke series_rolling_udf('num', 'rolling_max', 15, 'max', dynamic([null]))
| invoke series_rolling_udf('num', 'rolling_pct', 15, 'percentile', dynamic([75]))
| render timechart
```

:::image type="content" source="images/series-rolling-udf/graph-rolling-15.png" alt-text="30 개 요소의 롤링 min, max & 75 번째 백분위 수를 보여 주는 그래프" border="false":::

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
//
//  Calculate rolling trimmed mean
//
range x from 1 to 100 step 1
| extend y=iff(x % 13 == 0, 2.0, iff(x % 23 == 0, -2.0, rand()))
| summarize x=make_list(x), y=make_list(y)
| extend yr = dynamic(null)
| invoke series_rolling_udf('y', 'yr', 7, 'tmean', pack_array(pack_array(-2, 2), pack_array(false, false))) //  trimmed mean: ignoring values outside [-2,2] inclusive
| render linechart
```

:::image type="content" source="images/series-rolling-udf/rolling-trimmed-mean.png" alt-text="롤링 트리밍 평균을 보여 주는 그래프" border="false":::
