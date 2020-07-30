---
title: series_fill_forward ()-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 series_fill_forward ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 711a8eb851e7df8ba5b78bd03c42a77eb05d8510
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87344505"
---
# <a name="series_fill_forward"></a>series_fill_forward()

계열의 누락 값에 대 한 전방 채우기 보간을 수행 합니다.

동적 숫자 배열을 포함 하는 식은 입력입니다. 함수는 missing_value_placeholder의 모든 인스턴스를 missing_value_placeholder이 아닌 왼쪽의 가장 가까운 값으로 바꾸고 결과 배열을 반환 합니다. Missing_value_placeholder의 맨 왼쪽 인스턴스는 그대로 유지 됩니다.

## <a name="syntax"></a>Syntax

`series_fill_forward(`*x* `[, ` *missing_value_placeholder*`])`
* 는 *missing_value_placeholder* 채워진 전달의 모든 인스턴스가 포함 된 series *x* 를 반환 합니다.

## <a name="arguments"></a>인수

* *x*: 숫자 값 배열인 동적 배열 스칼라 식입니다. 
* *missing_value_placeholder*: 대체 될 누락 값에 대 한 자리 표시자를 지정 하는 선택적 매개 변수입니다. 기본값은 `double` (*null*)입니다.

**참고**

* [계열](make-seriesoperator.md)후 보간 함수를 적용 하려면 *null* 을 기본값으로 지정 합니다. 

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
make-series num=count() default=long(null) on TimeStamp from ago(1d) to ago(1h) step 1h by Os, Browser
```

* 실제 요소 형식으로 변환 되는 모든 형식을 *missing_value_placeholder* 수 있습니다. `double`(Null*null*) `long` (*null*) 및 `int` (*null*) 모두 동일한 의미를 갖습니다.
* Missing_value_placeholder가 (null) 이거나 동일한 의미를 갖는 생략 된 경우 결과에는 *null* 값이 포함 될 수 있습니다. 이러한 *null* 값을 채우려면 다른 보간 함수를 사용 합니다. 현재는 [series_outliers ()](series-outliersfunction.md) 만 입력 배열에서 *null* 값을 지원 합니다.
* 함수는 원래 형식의 배열 요소를 유지 합니다.

## <a name="example"></a>예제

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let data = datatable(arr: dynamic)
[
    dynamic([null,null,36,41,null,null,16,61,33,null,null])   
];
data 
| project arr, 
          fill_forward = series_fill_forward(arr)  

```

|`arr`|`fill_forward`|
|---|---|
|[null, null, 36, 41, null, null, 16, 61, 33, null, null]|[null, null, 36, 41, 41, 41, 16, 61, 33, 33, 33]|
   
위의 배열의 보간을 완료 하려면 [series_fill_backward](series-fill-backwardfunction.md) 또는 [계열 채우기-const](series-fill-constfunction.md) 를 사용 합니다.
