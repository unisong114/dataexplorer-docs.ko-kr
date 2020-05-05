---
title: series_fill_backward ()-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 series_fill_backward ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 211ac6cb078e2f61243f4616f9141a6f4834d464
ms.sourcegitcommit: 4f68d6dbfa6463dbb284de0aa17fc193d529ce3a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82741814"
---
# <a name="series_fill_backward"></a>series_fill_backward()

계열의 누락 값에 대해 뒤로 채우기 보간을 수행 합니다.

동적 숫자 배열을 포함 하는 식은 입력입니다. 함수는 missing_value_placeholder의 모든 인스턴스를 missing_value_placeholder를 제외 하 고 오른쪽의 가장 가까운 값으로 바꾸고 결과 배열을 반환 합니다. Missing_value_placeholder의 맨 오른쪽 인스턴스가 유지 됩니다.

**구문**

`series_fill_backward(`*x*`[, `*missing_value_placeholder*`])`
* 는 뒤로 채워지는 *missing_value_placeholder* 의 모든 인스턴스가 포함 된 series *x* 를 반환 합니다.

**인수**

* *x*: 숫자 값 배열인 동적 배열 스칼라 식입니다.
* *missing_value_placeholder*:이 선택적 매개 변수는 누락 값에 대 한 자리 표시자를 지정 합니다. 기본값은 `double`(*null*)입니다.

**참고 사항**

* [시리즈](make-seriesoperator.md)를 만든 후에 보간 함수를 적용 하려면 기본값으로 *null* 을 지정 합니다. 

```kusto
make-series num=count() default=long(null) on TimeStamp in range(ago(1d), ago(1h), 1h) by Os, Browser
```

* 실제 요소 형식으로 변환 되는 모든 형식을 *missing_value_placeholder* 수 있습니다. ( `double`*Null*), `long`(*null*) 및 `int`(*null*) 모두 동일한 의미를 갖습니다.
* *Missing_value_placeholder* 가 ( `double`*null*) 이거나 동일한 의미를 갖는 생략 된 경우 결과에는 *null* 값이 포함 될 수 있습니다. 이러한 *null* 값을 채우려면 다른 보간 함수를 사용 합니다. 현재는 [series_outliers ()](series-outliersfunction.md) 만 입력 배열에서 *null* 값을 지원 합니다.
* 함수는 원래 형식의 배열 요소를 유지 합니다.

**예제**

```kusto
let data = datatable(arr: dynamic)
[
    dynamic([111,null,36,41,null,null,16,61,33,null,null])   
];
data 
| project arr, 
          fill_forward = series_fill_backward(arr)

```

|`arr`|`fill_forward`|
|---|---|
|[111, null, 36, 41, null, null, 16, 61, 33, null, null]|[111, 36, 36, 41, 16, 16, 16, 61, 33, null, null]|

  
위의 배열의 보간을 완료 하려면 [series_fill_forward](series-fill-forwardfunction.md) 또는 [계열 채우기-const](series-fill-constfunction.md) 를 사용 합니다.
