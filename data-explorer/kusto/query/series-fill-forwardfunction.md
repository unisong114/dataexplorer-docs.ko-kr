---
title: series_fill_forward() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 series_fill_forward()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 6c79733aa1abf001f52eb07606c866904e370906
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81508673"
---
# <a name="series_fill_forward"></a>series_fill_forward()

계열에서 누락된 값의 정방향 채우기 보간을 수행합니다.

동적 숫자 배열을 포함하는 식을 입력으로 가져와 missing_value_placeholder 모든 인스턴스를 missing_value_placeholder 제외한 왼쪽에서 가장 가까운 값으로 바꾸고 결과 배열을 반환합니다. missing_value_placeholder 가장 왼쪽 인스턴스는 유지됩니다.

**구문**

`series_fill_forward(`*x*`[, `*missing_value_placeholder*`])`
* *missing_value_placeholder* 채워진 전달의 모든 인스턴스와 함께 시리즈 *x를* 반환합니다.

**인수**

* *x*: 숫자 값의 배열인 동적 배열 스칼라 식입니다. 
* *missing_value_placeholder*: 누락된 값을 대체할 자리 표시자를 지정하는 선택적 매개 변수입니다. 기본값은 `double`*(null)입니다.*

**참고 사항**

* [make-series](make-seriesoperator.md) 후 보간 함수를 적용하려면 *null을* 기본값으로 지정하는 것이 좋습니다. 

```kusto
make-series num=count() default=long(null) on TimeStamp in range(ago(1d), ago(1h), 1h) by Os, Browser
```

* *missing_value_placeholder* 실제 요소 유형으로 변환되는 모든 형식일 수 있습니다. 따라서 `double` `long`*(null), (null)* 또는*null* `int`*(null)* 같은 의미를 갖는다.
* *missing_value_placeholder* `double`*(null)(또는*동일한 의미를 가진 생략)인 경우 결과에 *null* 값이 포함될 수 있습니다. 다른 보간 함수를 사용하여 채웁니다. 현재 [series_outliers()](series-outliersfunction.md) 만 입력 배열에서 *null* 값을 지원합니다.
* 함수는 배열 요소의 원래 형식을 유지합니다.

**예제**

```kusto
let data = datatable(arr: dynamic)
[
    dynamic([null,null,36,41,null,null,16,61,33,null,null])   
];
data 
| project arr, 
          fill_forward = series_fill_forward(arr)  

```

|도착|fill_forward|
|---|---|
|[null,null,36,41, null, null, 16,61,33, null, null]|[null,null,36,41,41,41,41,16,61,33,33,33,33]|
   
위의 배열의 보간을 완료하기 위해 [series_fill_backward](series-fill-backwardfunction.md) 또는 [계열 채우기 구성을](series-fill-constfunction.md) 사용할 수 있습니다.