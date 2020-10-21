---
title: series_fill_linear ()-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 series_fill_linear ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 01a2e5dfee4f68a0a5aee55946960e8cc0fd25cd
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92250787"
---
# <a name="series_fill_linear"></a>series_fill_linear()

계열에서 누락 된 값을 선형으로 보간합니다.

동적 숫자 배열을 포함 하는 식을 입력으로 사용 하 고, missing_value_placeholder의 모든 인스턴스에 대해 선형 보간을 수행 하 고, 결과 배열을 반환 합니다. 배열의 시작과 끝에 missing_value_placeholder 포함 된 경우 missing_value_placeholder 이외의 가장 가까운 값으로 대체 됩니다. 이 기능을 해제할 수 있습니다. 전체 배열이 missing_value_placeholder로 구성 된 경우에는 배열이 constant_value으로 채워지거나, 지정 되지 않은 경우 0이 됩니다.  

## <a name="syntax"></a>구문

`series_fill_linear(`*x* `[,` *missing_value_placeholder* ` [,` *fill_edges* ` [,` *constant_value*`]]]))`
* 지정 된 매개 변수를 사용 하 여 *x* 의 계열 선형 보간을 반환 합니다.
 

## <a name="arguments"></a>인수

* *x*: 숫자 값 배열인 동적 배열 스칼라 식입니다.
* *missing_value_placeholder*: 대체할 "누락 값"에 대 한 자리 표시자를 지정 하는 선택적 매개 변수입니다. 기본값은 `double` (*null*)입니다.
* *fill_edges*: 배열의 시작과 끝에 있는 *missing_value_placeholder* 를 가장 가까운 값으로 바꾸어야 하는지 여부를 나타내는 부울 값입니다. 기본적으로 *True* 입니다. *False*로 설정 하면 배열의 시작과 끝에 있는 *missing_value_placeholder* 유지 됩니다.
* *constant_value*: 배열에만 관련 된 선택적 매개 변수는 모두 *null* 값으로 구성 됩니다. 이 매개 변수는 계열을 채울 상수 값을 지정 합니다. 기본값은 *0*입니다. 이 매개 변수를 `double` (*null*)로 설정 하면 *null* 값은 그대로 유지 됩니다.

## <a name="notes"></a>메모

* [시리즈](make-seriesoperator.md)를 만든 후에 보간 함수를 적용 하려면 *null* 을 기본값으로 지정 합니다. 

    <!-- csl: https://help.kusto.windows.net:443/Samples -->
    ```kusto
    make-series num=count() default=long(null) on TimeStamp from ago(1d) to ago(1h) step 1h by Os, Browser
    ```

* 실제 요소 형식으로 변환 되는 모든 형식을 *missing_value_placeholder* 수 있습니다. 이와 같이 `double` (*null*), `long` (*null*) 또는 `int` (*null*)는 동일한 의미를 갖습니다.
* *Missing_value_placeholder* 가 `double` (*null*) 이거나 동일한 의미를 갖는 생략 된 경우 결과에는 *null* 값이 포함 될 수 있습니다. 다른 보간 함수를 사용 하 여 이러한 *null* 값을 채웁니다. 현재는 [series_outliers ()](series-outliersfunction.md) 만 입력 배열에서 *null* 값을 지원 합니다.
* 함수는 원래 형식의 배열 요소를 유지 합니다. X에 int 또는 long 요소만 포함 된 경우 선형 보간은 정확한 값이 아니라 반올림 된 보간된 값을 반환 합니다.

## <a name="example"></a>예제

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let data = datatable(arr: dynamic)
[
    dynamic([null, 111.0, null, 36.0, 41.0, null, null, 16.0, 61.0, 33.0, null, null]), // Array of double    
    dynamic([null, 111,   null, 36,   41,   null, null, 16,   61,   33,   null, null]), // Similar array of int
    dynamic([null, null, null, null])                                                   // Array with missing values only
];
data
| project arr, 
          without_args = series_fill_linear(arr),
          with_edges = series_fill_linear(arr, double(null), true),
          wo_edges = series_fill_linear(arr, double(null), false),
          with_const = series_fill_linear(arr, double(null), true, 3.14159)  

```

|`arr`|`without_args`|`with_edges`|`wo_edges`|`with_const`|
|---|---|---|---|---|
|[null, 111.0, null, 36.0, 41.0, null, null, 16.0, 61.0, 33.0, null, null]|[111.0, 111.0, 73.5, 36.0, 41.0, 32.667, 24.333, 16.0, 61.0, 33.0, 33.0, 33.0]|[111.0, 111.0, 73.5, 36.0, 41.0, 32.667, 24.333, 16.0, 61.0, 33.0, 33.0, 33.0]|[null, 111.0, 73.5, 36.0, 41.0, 32.667, 24.333, 16.0, 61.0, 33.0, null, null]|[111.0, 111.0, 73.5, 36.0, 41.0, 32.667, 24.333, 16.0, 61.0, 33.0, 33.0, 33.0]|
|[null, 111, null, 36, 41, null, null, 16, 61, 33, null, null]|[111111, 73, 36, 41, 32, 24, 16, 61, 33, 33, 33]|[111111, 73, 36, 41, 32, 24, 16, 61, 33, 33, 33]|[null, 111, 73, 36, 41, 32, 24, 16, 61, 33, null, null]|[111111, 74, 38, 41, 32, 24, 16, 61, 33, 33, 33]|
|[null, null, null, null]|[0.0, 0.0, 0.0, 0.0]|[0.0, 0.0, 0.0, 0.0]|[0.0, 0.0, 0.0, 0.0]|[3.14159, 3.14159, 3.14159, 3.14159]|
