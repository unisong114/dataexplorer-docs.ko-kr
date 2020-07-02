---
title: series_fill_const ()-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 series_fill_const ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: e078919af16a9d2f7dadba0a309932b3a39b6ced
ms.sourcegitcommit: e093e4fdc7dafff6997ee5541e79fa9db446ecaa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/01/2020
ms.locfileid: "85763250"
---
# <a name="series_fill_const"></a>series_fill_const()

계열의 누락 값을 지정 된 상수 값으로 바꿉니다.

동적 숫자 배열을 포함 하는 식을 입력으로 사용 하 고 missing_value_placeholder의 모든 인스턴스를 지정 된 constant_value로 바꾸고 결과 배열을 반환 합니다.

**구문**

`series_fill_const(`*x* `[, ` *constant_value* `[,` *missing_value_placeholder*`]])`
* 는 *missing_value_placeholder* 의 모든 인스턴스가 *constant_value*로 바뀐 series *x* 를 반환 합니다.

**인수**

* *x*: 숫자 값의 배열인 동적 배열 스칼라 식입니다.
* *constant_value*: 대체할 누락 값에 대 한 자리 표시자를 지정 하는 매개 변수입니다. 기본값은 *0*입니다. 
* *missing_value_placeholder*: 교체할 누락 값에 대 한 자리 표시자를 지정 하는 선택적 매개 변수입니다. 기본값은 `double` (*null*)입니다.

**참고**
* DefaultValue 구문을 사용 하 여 상수 값으로 채워지는 계열을 만들 수 있습니다 `default = ` *DefaultValue* (또는 0이 아닌 것으로 가정). 자세한 내용은 [시리즈 만들기](make-seriesoperator.md)를 참조 하세요.

```kusto
make-series num=count() default=-1 on TimeStamp from ago(1d) to ago(1h) step 1h by Os, Browser
```
  
* [시리즈](make-seriesoperator.md)를 만든 후에 보간 함수를 적용 하려면 *null* 을 기본값으로 지정 합니다. 

```kusto
make-series num=count() default=long(null) on TimeStamp from ago(1d) to ago(1h) step 1h by Os, Browser
```
  
* *Missing_value_placeholder* 은 실제 요소 형식으로 변환 되는 모든 형식일 수 있습니다. 이와 같이 `double` (*null*), `long` (*null*) 또는 `int` (*null*)는 동일한 의미를 갖습니다.
* 함수는 배열 요소의 원래 형식을 유지 합니다. 

**예제**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let data = datatable(`arr`: dynamic)
[
    dynamic([111,null,36,41,23,null,16,61,33,null,null])   
];
data 
| project arr, 
          fill_const1 = series_fill_const(arr, 0.0),
          fill_const2 = series_fill_const(arr, -1)  
```

|`arr`|`fill_const1`|`fill_const2`|
|---|---|---|
|[111, null, 36, 41, 23, null, 16, 61, 33, null, null]|[111, 0.0, 36, 41, 23, 0.0, 16, 61, 33, 0.0, 0.0]|[111,-1, 36, 41, 23,-1, 16, 61, 33,-1,-1]|
