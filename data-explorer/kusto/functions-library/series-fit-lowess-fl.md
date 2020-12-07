---
title: series_fit_lowess_fl ()-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 series_fit_lowess_fl () 사용자 정의 함수에 대해 설명 합니다.
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: reference
ms.date: 11/29/2020
no-loc: LOWESS
ms.openlocfilehash: da384b1a907e4b524fc40f1c4133be5cf8e21c9c
ms.sourcegitcommit: 4d5628b52b84f7564ea893f621bdf1a45113c137
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2020
ms.locfileid: "96469766"
---
# <a name="series_fit_lowess_fl"></a>series_fit_lowess_fl()

함수는 `series_fit_lowess_fl()` 계열에 [ LOWESS 회귀](https://www.wikipedia.org/wiki/Local_regression) 를 적용 합니다. 이 함수는 여러 계열 (동적 숫자 배열)이 있는 테이블을 사용 하 고 *LOWESS 곡선* 을 생성 하며이는 원래 계열의 곡선입니다.

> [!NOTE]
> * `series_fit_lowess_fl()` 는 [UDF (사용자 정의 함수)](../query/functions/user-defined-functions.md)입니다.
> * 이 함수는 인라인 Python을 포함 하며 클러스터에서 [python () 플러그 인을 사용 하도록 설정](../query/pythonplugin.md#enable-the-plugin) 해야 합니다. 자세한 내용은 [사용](#usage)을 참조 하세요.

## <a name="syntax"></a>Syntax

`T | invoke series_fit_lowess_fl(`*y_series* `,` *y_fit_series* `, [` *fit_size* `, ` *x_series* `,` *x_istime*]`)`

## <a name="arguments"></a>인수

* *y_series*: [종속 변수](https://www.wikipedia.org/wiki/Dependent_and_independent_variables)를 포함 하는 입력 테이블 열의 이름입니다. 이 열은 맞출 계열입니다.
* *y_fit_series*: 적합 한 계열을 저장할 열의 이름입니다.
* *fit_size*: 각 지점에 대해 각각의 *fit_size* 가장 가까운 지점에 로컬 회귀를 적용 합니다. 이 매개 변수는 선택 사항이 며 기본값은 *5* 입니다.
* *x_series*: [독립 변수](https://www.wikipedia.org/wiki/Dependent_and_independent_variables)를 포함 하는 열의 이름입니다 (x 또는 time 축). 이 매개 변수는 선택 사항이 며 [균일 하지 않게 간격이](https://www.wikipedia.org/wiki/Unevenly_spaced_time_series)지정 된 계열에만 필요 합니다. 기본 값은 빈 문자열입니다. x는 균일 하 게 배치 된 계열의 회귀에 대해 중복 됩니다.
* *x_istime*:이 부울 매개 변수는 *x_series* 를 지정 하 고 datetime의 벡터 인 경우에만 필요 합니다. 이 매개 변수는 선택 사항이 며 기본값은 *False* 입니다.

## <a name="usage"></a>사용량

`series_fit_lowess_fl()`[호출 연산자](../query/invokeoperator.md)를 사용 하 여 적용 되는 사용자 정의 함수 [테이블 형식 함수](../query/functions/user-defined-functions.md#tabular-function)입니다. 쿼리에 해당 코드를 포함 하거나 데이터베이스에 설치할 수 있습니다. Ad hoc 및 영구 사용의 두 가지 사용 옵션이 있습니다. 예제는 아래 탭을 참조 하세요.

# <a name="ad-hoc"></a>[임시](#tab/adhoc)

임시 사용의 경우 [let 문을](../query/letstatement.md)사용 하 여 해당 코드를 포함 합니다. 권한이 필요 하지 않습니다.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let series_fit_lowess_fl=(tbl:(*), y_series:string, y_fit_series:string, fit_size:int=5, x_series:string='', x_istime:bool=False)
{
    let kwargs = pack('y_series', y_series, 'y_fit_series', y_fit_series, 'fit_size', fit_size, 'x_series', x_series, 'x_istime', x_istime);
    let code=
        '\n'
        'y_series = kargs["y_series"]\n'
        'y_fit_series = kargs["y_fit_series"]\n'
        'fit_size = kargs["fit_size"]\n'
        'x_series = kargs["x_series"]\n'
        'x_istime = kargs["x_istime"]\n'
        '\n'
        'import statsmodels.api as sm\n'
        'def lowess_fit(ts_row, x_col, y_col, fsize):\n'
        '    y = ts_row[y_col]\n'
        '    fraction = fsize/len(y)\n'
        '    if x_col == "": # If there is no x column creates sequential range [1, len(y)]\n'
        '       x = np.arange(len(y)) + 1\n'
        '    else: # if x column exists check whether its a time column. If so, normalize it to the [1, len(y)] range, else take it as is.\n'
        '       if x_istime: \n'
        '           x = pd.to_numeric(pd.to_datetime(ts_row[x_col]))\n'
        '           x = x - x.min()\n'
        '           x = x / x.max()\n'
        '           x = x * (len(x) - 1) + 1\n'
        '       else:\n'
        '           x = ts_row[x_col]\n'
        '    lowess = sm.nonparametric.lowess\n'
        '    z = lowess(y, x, return_sorted=False, frac=fraction)\n'
        '    return list(z)\n'
        '\n'
        'result = df\n'
        'result[y_fit_series] = df.apply(lowess_fit, axis=1, args=(x_series, y_series, fit_size))\n'
    ;
    tbl
     | evaluate python(typeof(*), code, kwargs)
};
//
// Apply 9 points LOWESS regression on regular time series
//
let max_t = datetime(2016-09-03);
demo_make_series1
| make-series num=count() on TimeStamp from max_t-1d to max_t step 5m by OsVer
| extend fnum = dynamic(null)
| invoke series_fit_lowess_fl('num', 'fnum', 9)
| render timechart
```

# <a name="persistent"></a>[Persistent](#tab/persistent)

영구적으로 사용 하려면을 사용 [`.create function`](../management/create-function.md) 합니다.  함수를 만들려면 [데이터베이스 사용자 권한이](../management/access-control/role-based-authorization.md)필요 합니다.

### <a name="one-time-installation"></a>일회성 설치

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
.create-or-alter function with (folder = "Packages\\Series", docstring = "Fits a local polynomial using LOWESS method to a series")
series_fit_lowess_fl(tbl:(*), y_series:string, y_fit_series:string, fit_size:int=5, x_series:string='', x_istime:bool=False)
{
    let kwargs = pack('y_series', y_series, 'y_fit_series', y_fit_series, 'fit_size', fit_size, 'x_series', x_series, 'x_istime', x_istime);
    let code=
        '\n'
        'y_series = kargs["y_series"]\n'
        'y_fit_series = kargs["y_fit_series"]\n'
        'fit_size = kargs["fit_size"]\n'
        'x_series = kargs["x_series"]\n'
        'x_istime = kargs["x_istime"]\n'
        '\n'
        'import statsmodels.api as sm\n'
        'def lowess_fit(ts_row, x_col, y_col, fsize):\n'
        '    y = ts_row[y_col]\n'
        '    fraction = fsize/len(y)\n'
        '    if x_col == "": # If there is no x column creates sequential range [1, len(y)]\n'
        '       x = np.arange(len(y)) + 1\n'
        '    else: # if x column exists check whether its a time column. If so, normalize it to the [1, len(y)] range, else take it as is.\n'
        '       if x_istime: \n'
        '           x = pd.to_numeric(pd.to_datetime(ts_row[x_col]))\n'
        '           x = x - x.min()\n'
        '           x = x / x.max()\n'
        '           x = x * (len(x) - 1) + 1\n'
        '       else:\n'
        '           x = ts_row[x_col]\n'
        '    lowess = sm.nonparametric.lowess\n'
        '    z = lowess(y, x, return_sorted=False, frac=fraction)\n'
        '    return list(z)\n'
        '\n'
        'result = df\n'
        'result[y_fit_series] = df.apply(lowess_fit, axis=1, args=(x_series, y_series, fit_size))\n'
    ;
    tbl
     | evaluate python(typeof(*), code, kwargs)
}
```

### <a name="usage"></a>사용량

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
//
// Apply 9 points LOWESS regression on regular time series
//
let max_t = datetime(2016-09-03);
demo_make_series1
| make-series num=count() on TimeStamp from max_t-1d to max_t step 5m by OsVer
| extend fnum = dynamic(null)
| invoke series_fit_lowess_fl('num', 'fnum', 9)
| render timechart
```

---

:::image type="content" source="images/series-fit-lowess-fl/lowess-regular-time-series.png" alt-text="Graph showing nine points :::no-loc (LOWESS)::: regular 시계열에 맞추기 "border =" false ":::

## <a name="examples"></a>예제

다음 예에서는 함수가 이미 설치 되어 있다고 가정 합니다.

### <a name="test-irregular-time-series"></a>불규칙 한 시계열 테스트

다음 예제에서는 불규칙 (균일 하지 않게) 시계열을 테스트 합니다.
    
    <!-- csl: https://help.kusto.windows.net:443/Samples -->
    ```kusto
    let max_t = datetime(2016-09-03);
    demo_make_series1
    | where TimeStamp between ((max_t-1d)..max_t)
    | summarize num=count() by bin(TimeStamp, 5m), OsVer
    | order by TimeStamp asc
    | where hourofday(TimeStamp) % 6 != 0   //  delete every 6th hour to create irregular time series
    | summarize TimeStamp=make_list(TimeStamp), num=make_list(num) by OsVer
    | extend fnum = dynamic(null)
    | invoke series_fit_lowess_fl('num', 'fnum', 9, 'TimeStamp', True)
    | render timechart 
    ```
    
    :::image type="content" source="images/series-fit-lowess-fl/lowess-irregular-time-series.png" alt-text="Graph showing nine points LOWESS fit to an irregular time series" border="false":::

비교 LOWESS 및 다항식 맞추기

다음 예제에는 x 및 y 축에 노이즈가 있는 다섯 번째 순서 다항식이 포함 되어 있습니다. 비교와 다항식의 일치를 참조 하세요 LOWESS . 

    <!-- csl: https://help.kusto.windows.net:443/Samples -->
    ```kusto
    range x from 1 to 200 step 1
    | project x = rand()*5 - 2.3
    | extend y = pow(x, 5)-8*pow(x, 3)+10*x+6
    | extend y = y + (rand() - 0.5)*0.5*y
    | summarize x=make_list(x), y=make_list(y)
    | extend y_lowess = dynamic(null)
    | invoke series_fit_lowess_fl('y', 'y_lowess', 15, 'x')
    | extend series_fit_poly(y, x, 5)
    | project x, y, y_lowess, y_polynomial=series_fit_poly_y_poly_fit
    | render linechart
    ```
        
    :::image type="content" source="images/series-fit-lowess-fl/lowess-vs-poly-fifth-order-noise.png" alt-text="Graphs of LOWESS vs polynomial fit for a fifth order polynomial with noise on x & y axes":::
