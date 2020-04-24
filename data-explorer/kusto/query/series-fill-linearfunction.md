---
title: series_fill_linear() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 series_fill_linear()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 4f9a12d172a1580d6a9e575b78b14404dce7820e
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81508656"
---
# <a name="series_fill_linear"></a>series_fill_linear()

계열에서 누락된 값의 선형 보간을 수행합니다.

동적 숫자 배열을 포함하는 식을 입력으로 가져와 missing_value_placeholder 모든 인스턴스에 대해 선형 보간을 수행하고 결과 배열을 반환합니다. 배열의 시작과 끝에 missing_value_placeholder 포함되어 있으면 missing_value_placeholder 이외의 가장 가까운 값으로 대체됩니다(끌 수 있음). 전체 배열이 missing_value_placeholder 구성되면 배열은 지정되지 않은 경우 constant_value 또는 0으로 채워집니다.  

**구문**

`series_fill_linear(`*x* `[,` *missing_value_placeholder*` [,`*fill_edges*` [,`*constant_value*`]]]))`
* 지정된 매개 변수를 사용하여 *x의* 직렬 선형 보간을 반환합니다.
 

**인수**

* *x*: 숫자 값의 배열인 동적 배열 스칼라 식입니다.
* *missing_value_placeholder*: 대체할 "누락된 값"에 대한 자리 표시자를 지정하는 선택적 매개 변수입니다. 기본값은 `double`*(null)입니다.*
* *fill_edges*: 배열의 시작과 끝에 *missing_value_placeholder* 가장 가까운 값으로 대체해야 하는지 여부를 나타내는 부울 값입니다. 기본적으로 *True입니다.* *false로* 설정하면 배열의 시작과 끝에 *missing_value_placeholder* 유지됩니다.
* *constant_value*: 배열에만 관련된 선택적 매개 변수는 전적으로 *null* 값으로 구성되며, 이는 시리즈를 채우기 위해 상수 값을 지정합니다. 기본값은 *0입니다.* 이 매개 변수를 `double`*(null)로*설정하면 *null* 값이 있는 위치에 효과적으로 남게 됩니다.

**참고 사항**

* [make-series](make-seriesoperator.md) 후 보간 함수를 적용하려면 *null을* 기본값으로 지정하는 것이 좋습니다. 

```kusto
make-series num=count() default=long(null) on TimeStamp in range(ago(1d), ago(1h), 1h) by Os, Browser
```

* *missing_value_placeholder* 실제 요소 유형으로 변환되는 모든 형식일 수 있습니다. 따라서 `double` `long`*(null), (null)* 또는*null* `int`*(null)* 같은 의미를 갖는다.
* *missing_value_placeholder* `double`*(null)(또는*동일한 의미를 가진 생략)인 경우 결과에 *null* 값이 포함될 수 있습니다. 다른 보간 함수를 사용하여 채웁니다. 현재 [series_outliers()](series-outliersfunction.md) 만 입력 배열에서 *null* 값을 지원합니다.
* 함수는 배열 요소의 원래 형식을 유지합니다. *x에* *int* 또는 *long* 요소만 포함된 경우 선형 보간은 정확한 보간값이 아닌 반올림된 보간 값을 반환합니다.

**예제**

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

|도착|without_args|with_edges|wo_edges|with_const|
|---|---|---|---|---|
|[null, 111.0, null, 36.0, 41.0, null, null, 16.0,61.0, 33.0, null, null]|[111.0,111.0,73.5,36.0,41.0,32.667,24.333,16.0,61.0,33.0,33.0,33.0]|[111.0,111.0,73.5,36.0,41.0,32.667,24.333,16.0,61.0,33.0,33.0,33.0]|[null, 111.0, 73.5,36.0,41.0,32.667,24.333,16.0,61.0,33.0,null]|[111.0,111.0,73.5,36.0,41.0,32.667,24.333,16.0,61.0,33.0,33.0,33.0]|
|[null,111,null,36,41,null,null,16,61,33,null]|[111,111,73,36,41,32,24,16,61,33,33,33]|[111,111,73,36,41,32,24,16,61,33,33,33]|[null,111,73,36,41,32,24,16,61,33,null]|[111,111,74,38, 41,32,24,16,61,33,33,33]|
|[null, null, null, null]|[0.0,0.0,0.0,0.0]|[0.0,0.0,0.0,0.0]|[0.0,0.0,0.0,0.0]|[3.14159,3.14159,3.14159,3.14159]|