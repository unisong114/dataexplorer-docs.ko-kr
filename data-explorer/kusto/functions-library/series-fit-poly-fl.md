---
title: series_fit_poly_fl ()-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 series_fit_poly_fl () 사용자 정의 함수에 대해 설명 합니다.
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: reference
ms.date: 09/08/2020
ms.openlocfilehash: 3834e3189a62cd8581d2e3607acad9c7d8c8d7c2
ms.sourcegitcommit: 50c799c60a3937b4c9e81a86a794bdb189df02a3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/14/2020
ms.locfileid: "90075230"
---
# <a name="series_fit_poly_fl"></a>series_fit_poly_fl ()

함수는 `series_fit_poly_fl()` 계열에 다항식 회귀를 적용 합니다. 여러 계열 (동적 숫자 배열)을 포함 하는 테이블을 사용 하 고 각 계열에 대해 [다항식 회귀](https://en.wikipedia.org/wiki/Polynomial_regression)를 사용 하 여 가장 잘 맞는 큰 다항식을 생성 합니다. 이 함수는 계열의 범위에서 다항식 계수와 보간된 다항식을 모두 반환 합니다.

> [!NOTE]
> `series_fit_poly_fl()` 는 [UDF (사용자 정의 함수)](../query/functions/user-defined-functions.md)입니다. 이 함수는 인라인 Python을 포함 하며 클러스터에서 [python () 플러그 인을 사용 하도록 설정](../query/pythonplugin.md#enable-the-plugin) 해야 합니다. 자세한 내용은 [사용](#usage)을 참조 하세요. [시리즈 연산자](../query/make-seriesoperator.md)를 사용 하 여 만든 것과 같이 균일 하 게 배치 된 계열의 선형 회귀의 경우 native 함수 [series_fit_line ()](../query/series-fit-linefunction.md)를 사용 합니다.

## <a name="syntax"></a>구문

`T | invoke series_fit_poly_fl(`*y_series* `,` *y_fit_series* `,` *fit_coeff* `,` *수준* `, [` *x_series* `,` *x_istime*]`)`
  
## <a name="arguments"></a>인수

* *y_series*: [종속 변수](https://en.wikipedia.org/wiki/Dependent_and_independent_variables)를 포함 하는 입력 테이블 열의 이름입니다. 즉, 적합 한 계열입니다.
* *y_fit_series*: 최적 맞춤 계열을 저장할 열의 이름입니다.
* *fit_coeff*: 가장 적합 한 다항식 계수를 저장할 열의 이름입니다.
* *수준*: 크기를 조정 하는 데 필요한 다항식의 순서입니다. 예를 들어 선형 회귀의 경우 1, 2 차 회귀의 경우 2입니다.
* *x_series*: [독립 변수](https://en.wikipedia.org/wiki/Dependent_and_independent_variables)를 포함 하는 열의 이름입니다 (x 또는 time 축). 이 매개 변수는 선택 사항이 며 [균일 하지 않게 간격이](https://en.wikipedia.org/wiki/Unevenly_spaced_time_series)지정 된 계열에만 필요 합니다. 기본 값은 빈 문자열입니다. x는 균일 하 게 배치 된 계열의 회귀에 대해 중복 됩니다.
* *x_istime*:이 부울 매개 변수는 선택 사항입니다. 이 매개 변수는 *x_series* 지정 되 고 datetime의 벡터 인 경우에만 필요 합니다.

## <a name="usage"></a>사용량

`series_fit_poly_fl()`[호출 연산자](../query/invokeoperator.md)를 사용 하 여 적용 되는 사용자 정의 함수 [테이블 형식 함수](../query/functions/user-defined-functions.md#tabular-function)입니다. 쿼리에 해당 코드를 포함 하거나 데이터베이스에 설치할 수 있습니다. Ad hoc 및 영구 사용의 두 가지 사용 옵션이 있습니다. 예제는 아래 탭을 참조 하세요.

# <a name="ad-hoc"></a>[임시](#tab/adhoc)

임시 사용의 경우 [let 문을](../query/letstatement.md)사용 하 여 해당 코드를 포함 합니다. 권한이 필요 하지 않습니다.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let series_fit_poly_fl=(tbl:(*), y_series:string, y_fit_series:string, fit_coeff:string, degree:int, x_series:string='', x_istime:bool=False)
{
    let kwargs = pack('y_series', y_series, 'y_fit_series', y_fit_series, 'fit_coeff', fit_coeff, 'degree', degree, 'x_series', x_series, 'x_istime', x_istime);
    let code=
        '\n'
        'y_series = kargs["y_series"]\n'
        'y_fit_series = kargs["y_fit_series"]\n'
        'fit_coeff = kargs["fit_coeff"]\n'
        'degree = kargs["degree"]\n'
        'x_series = kargs["x_series"]\n'
        'x_istime = kargs["x_istime"]\n'
        '\n'
        'def fit(ts_row, x_col, y_col, deg):\n'
        '    y = ts_row[y_col]\n'
        '    # if x column exists check whether its a time column. If so, convert it to numeric seconds, else take it as is. If there is no x column creates sequential numbers\n'
        '    if x_col == "":\n'
        '       x = np.arange(len(y))\n'
        '    else:\n'
        '       if x_istime:\n'
        '           x = pd.to_numeric(pd.to_datetime(ts_row[x_col]))/(1e9*60) #convert ticks to minutes\n'
        '           x = x - x.min()\n'
        '       else:\n'
        '           x = ts_row[x_col]\n'
        '    coeff = np.polyfit(x, y, deg)\n'
        '    p = np.poly1d(coeff)\n'
        '    z = p(x)\n'
        '    return z, coeff\n'
        '\n'
        'result = df\n'
        'if len(df):\n'
        '   result[[y_fit_series, fit_coeff]] = df.apply(fit, axis=1, args=(x_series, y_series, degree,), result_type="expand")\n'
    ;
    tbl
     | evaluate python(typeof(*), code, kwargs)
};
//
// Fit 5th order polynomial to a regular (evenly spaced) time series, created with make-series
//
let max_t = datetime(2016-09-03);
demo_make_series1
| make-series num=count() on TimeStamp from max_t-1d to max_t step 5m by OsVer
| extend fnum = dynamic(null), coeff=dynamic(null), fnum1 = dynamic(null), coeff1=dynamic(null)
| invoke series_fit_poly_fl('num', 'fnum', 'coeff', 5)
| render timechart with(ycolumns=num, fnum)
```

# <a name="persistent"></a>[Persistent](#tab/persistent)

영구적으로 사용 하려면 [. create 함수](../management/create-function.md)를 사용 합니다.  함수를 만들려면 [데이터베이스 사용자 권한이](../management/access-control/role-based-authorization.md)필요 합니다.

### <a name="one-time-installation"></a>일회성 설치

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
.create-or-alter function with (folder = "Packages\\Series", docstring = "Fit a polynomial of a specified degree to a series")
series_fit_poly_fl(tbl:(*), y_series:string, y_fit_series:string, fit_coeff:string, degree:int, x_series:string='', x_istime:bool=false)
{
    let kwargs = pack('y_series', y_series, 'y_fit_series', y_fit_series, 'fit_coeff', fit_coeff, 'degree', degree, 'x_series', x_series, 'x_istime', x_istime);
    let code=
        '\n'
        'y_series = kargs["y_series"]\n'
        'y_fit_series = kargs["y_fit_series"]\n'
        'fit_coeff = kargs["fit_coeff"]\n'
        'degree = kargs["degree"]\n'
        'x_series = kargs["x_series"]\n'
        'x_istime = kargs["x_istime"]\n'
        '\n'
        'def fit(ts_row, x_col, y_col, deg):\n'
        '    y = ts_row[y_col]\n'
        '    # if x column exists check whether its a time column. If so, convert it to numeric seconds, else take it as is. If there is no x column creates sequential numbers\n'
        '    if x_col == "":\n'
        '       x = np.arange(len(y))\n'
        '    else:\n'
        '       if x_istime:\n'
        '           x = pd.to_numeric(pd.to_datetime(ts_row[x_col]))/(1e9*60) #convert ticks to minutes\n'
        '           x = x - x.min()\n'
        '       else:\n'
        '           x = ts_row[x_col]\n'
        '    coeff = np.polyfit(x, y, deg)\n'
        '    p = np.poly1d(coeff)\n'
        '    z = p(x)\n'
        '    return z, coeff\n'
        '\n'
        'result = df\n'
        'if len(df):\n'
        '   result[[y_fit_series, fit_coeff]] = df.apply(fit, axis=1, args=(x_series, y_series, degree,), result_type="expand")\n'
    ;
    tbl
     | evaluate python(typeof(*), code, kwargs)
}
```

### <a name="usage"></a>사용량

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
//
// Fit 5th order polynomial to a regular (evenly spaced) time series, created with make-series
//
let max_t = datetime(2016-09-03);
demo_make_series1
| make-series num=count() on TimeStamp from max_t-1d to max_t step 5m by OsVer
| extend fnum = dynamic(null), coeff=dynamic(null), fnum1 = dynamic(null), coeff1=dynamic(null)
| invoke series_fit_poly_fl('num', 'fnum', 'coeff', 5)
| render timechart with(ycolumns=num, fnum)
```

---

:::image type="content" source="images/series-fit-poly-fl/usage-example.png" alt-text="일반 시계열에 맞는 5 번째 순서 다항식을 보여 주는 그래프" border="false":::

## <a name="additional-examples"></a>추가 예

다음 예에서는 함수가 이미 설치 되어 있다고 가정 합니다.

1. 불규칙 (균일 하지 않게) 시계열 테스트
    
    <!-- csl: https://help.kusto.windows.net:443/Samples -->
    ```kusto
    //
    //  Test irregular (unevenly spaced) time series
    //
    let max_t = datetime(2016-09-03);
    demo_make_series1
    | where TimeStamp between ((max_t-2d)..max_t)
    | summarize num=count() by bin(TimeStamp, 5m), OsVer
    | order by TimeStamp asc
    | where hourofday(TimeStamp) % 6 != 0   //  delete every 6th hour to create unevenly spaced time series
    | summarize TimeStamp=make_list(TimeStamp), num=make_list(num) by OsVer
    | extend fnum = dynamic(null), coeff=dynamic(null)
    | invoke series_fit_poly_fl('num', 'fnum', 'coeff', 8, 'TimeStamp', True)
    | render timechart with(ycolumns=num, fnum)
    ```
    
    :::image type="content" source="images/series-fit-poly-fl/irregular-time-series.png" alt-text="불규칙 한 시계열에 맞는 8 번째 순서 다항식을 보여 주는 그래프" border="false":::

1. x & y 축에서 노이즈가 있는 5 번째 순서 다항식

    <!-- csl: https://help.kusto.windows.net:443/Samples -->
    ```kusto
    //
    // 5th order polynomial with noise on x & y axes
    //
    range x from 1 to 200 step 1
    | project x = rand()*5 - 2.3
    | extend y = pow(x, 5)-8*pow(x, 3)+10*x+6
    | extend y = y + (rand() - 0.5)*0.5*y
    | order by x asc 
    | summarize x=make_list(x), y=make_list(y)
    | extend y_fit = dynamic(null), coeff=dynamic(null)
    | invoke series_fit_poly_fl('y', 'y_fit', 'coeff', 5, 'x')
    |fork (project-away coeff) (project coeff | mv-expand coeff)
    | render linechart
    ```
        
    :::image type="content" source="images/series-fit-poly-fl/fifth-order-noise.png" alt-text="X & y 축에 노이즈가 있는 5 번째 순서 다항식의 맞춤 그래프":::
       
    :::image type="content" source="images/series-fit-poly-fl/fifth-order-noise-table.png" alt-text="노이즈가 있는 5 번째 순서 다항식의 계수" border="false":::
