---
title: series_fill_const() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 series_fill_const()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: c7f5f939068737bdd6519fc0c63b663d19ae076a
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81508775"
---
# <a name="series_fill_const"></a>series_fill_const()

계열의 누락된 값을 지정된 상수 값으로 바꿉습니다.

동적 숫자 배열을 포함하는 식을 입력으로 가져와 missing_value_placeholder 모든 인스턴스를 지정된 constant_value 대체하고 결과 배열을 반환합니다.

**구문**

`series_fill_const(`*x*`[, `*constant_value* `[,` *missing_value_placeholder*`]])`
* missing_value_placeholder 모든 인스턴스가 *constant_value*대체된 *경우* 시리즈 *x를* 반환합니다.

**인수**

* *x*: 숫자 값의 배열인 동적 배열 스칼라 식입니다.
* *constant_value*: 누락된 값을 대체할 자리 표시자를 지정하는 매개 변수입니다. 기본값은 *0입니다.* 
* *missing_value_placeholder*: 누락된 값을 대체할 자리 표시자를 지정하는 선택적 매개 변수입니다. 기본값은 `double`*(null)입니다.*

**참고 사항**
* `default = ` *DefaultValue* 구문(또는 0을 가정하는 생략)을 사용하여 한 호출에서 일정한 채우기가 있는 계열을 만들 수 있습니다. 자세한 내용은 [메이크 시리즈를](make-seriesoperator.md) 참조하십시오.

```kusto
make-series num=count() default=-1 on TimeStamp in range(ago(1d), ago(1h), 1h) by Os, Browser
```
  
* [make-series](make-seriesoperator.md) 후 보간 함수를 적용하려면 *null을* 기본값으로 지정하는 것이 좋습니다. 

```kusto
make-series num=count() default=long(null) on TimeStamp in range(ago(1d), ago(1h), 1h) by Os, Browser
```
  
* *missing_value_placeholder* 실제 요소 유형으로 변환되는 모든 형식일 수 있습니다. 따라서 `double` `long`*(null), (null)* 또는*null* `int`*(null)* 같은 의미를 갖는다.
* 함수는 배열 요소의 원래 형식을 유지합니다. 

**예제**

```kusto
let data = datatable(arr: dynamic)
[
    dynamic([111,null,36,41,23,null,16,61,33,null,null])   
];
data 
| project arr, 
          fill_const1 = series_fill_const(arr, 0.0),
          fill_const2 = series_fill_const(arr, -1)  
```

|도착|fill_const1|fill_const2|
|---|---|---|
|[111,null,36,41,23,null,16,61,33,null, null]|[111,0.0,36,41,23,0.0,16,61,33,0.0,0.0]|[111,-1,36,41,23,-1,16,61,33,-1,-1]|