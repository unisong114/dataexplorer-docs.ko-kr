---
title: series_iir() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 series_iir()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/20/2019
ms.openlocfilehash: 96452265e07a8a8b057dc70bec520be6ab298dd3
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81508299"
---
# <a name="series_iir"></a>series_iir()

계열에 무한 임펄스 응답 필터를 적용합니다.  

동적 숫자 배열을 포함하는 식을 입력으로 가져와 무한 임펄스 응답 필터를 [적용합니다.](https://en.wikipedia.org/wiki/Infinite_impulse_response) 필터 계수를 지정하여, 예를 들어, 계열의 누적 합계를 계산하고, 스무딩 연산뿐만 아니라 다양한 [하이패스,](https://en.wikipedia.org/wiki/High-pass_filter) [대역패스](https://en.wikipedia.org/wiki/Band-pass_filter) 및 [로우 패스](https://en.wikipedia.org/wiki/Low-pass_filter) 필터를 적용하는 데 사용할 수 있습니다. 이 함수는 필터 a *및* *b* 계수의 동적 배열과 두 개의 정적 동적 배열을 포함하는 열의 입력으로 사용되며 열에 필터를 적용합니다. 필터링된 출력을 포함하는 새 동적 배열 열을 출력합니다.  
 

**구문**

`series_iir(`*x* `,` *b* `,` *a*`)`

**인수**

* *x*: 숫자 값의 배열인 동적 배열 셀로, 일반적으로 [메이크 계열](make-seriesoperator.md) 또는 [make_list](makelist-aggfunction.md) 연산자의 결과 출력입니다.
* *b*: 필터의 분자 계수를 포함하는 상수 식입니다(숫자 값의 동적 배열로 저장됨).
* *a*: *b와*같은 상수 식 . 필터의 분모 계수를 포함합니다.

> [!IMPORTANT]
> (즉,)의 `a` `a[0]`첫 번째 요소는 0이 되어서는 안됩니다(0으로 나누기 위해, 아래 수식 참조).

**필터의 재귀 수식에 대해 자세히 알아보기**

* 입력 배열 X와 계수 배열a, 길이의 bn_a 각각 과 n_b 감안할 때, 출력 배열 Y를 생성하는 필터의 전달 함수는 정의됩니다(위키백과에서도 참조).

<div align="center">
Y<sub>i</sub> =<sub>0</sub><sup>-1</sup>(b<sub>0</sub>X<sub>i b</sub> 
 + <sub>1</sub>X<sub>i-1</sub> + ... + b<sub>n<sub>b</sub>-1</sub>X<sub>i-n<sub>b</sub>-1</sub> 
 - <sub>a</sub><sub>2</sub>Y<sub>i-2</sub> - - n<sub><sub>a</sub>-1</sub>Y<sub>i-n<sub>a</sub>-1)</sub><sub>i-1</sub>
</div>

**예제**

누적 합계 계산은 계수 *a*=[1,-1] 및 *b*=[1]를 가진 iir 필터에 의해 수행될 수 있습니다.  

```kusto
let x = range(1.0, 10, 1);
print x=x, y = series_iir(x, dynamic([1]), dynamic([1,-1]))
| mv-expand x, y
```

| x | y |
|:--|:--|
|1.0|1.0|
|2.0|3.0|
|3.0|6.0|
|4.0|10.0|

함수에서 래핑하는 방법은 다음과 같습니다.

```kusto
let vector_sum=(x:dynamic)
{
  let y=array_length(x) - 1;
  toreal(series_iir(x, dynamic([1]), dynamic([1, -1]))[y])
};
print d=dynamic([0, 1, 2, 3, 4])
| extend dd=vector_sum(d)
```

|d            |dd  |
|-------------|----|
|`[0,1,2,3,4]`|`10`|