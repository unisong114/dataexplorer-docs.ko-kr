---
title: series_iir ()-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 series_iir ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/20/2019
ms.openlocfilehash: e1b863b83e08fae680e1a387ca2fdd2a93d111a8
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87351440"
---
# <a name="series_iir"></a>series_iir()

계열에 무한 임펄스 응답 필터를 적용 합니다.  

함수는 동적 숫자 배열을 포함 하는 식을 입력으로 사용 하 고 [무한 임펄스 응답](https://en.wikipedia.org/wiki/Infinite_impulse_response) 필터를 적용 합니다. 필터 계수를 지정 하 여 함수를 사용할 수 있습니다.
* 계열의 누적 합계를 계산 하려면
* 다듬기 작업을 적용 하려면
* 다양 한 [높은 통과](https://en.wikipedia.org/wiki/High-pass_filter), [대역 외](https://en.wikipedia.org/wiki/Band-pass_filter)통과 필터 및 [낮은 패스](https://en.wikipedia.org/wiki/Low-pass_filter) 의 필터를 적용 하려면

함수는 동적 배열과 필터 *a* 및 *b* 계수의 정적 동적 배열 두 개가 포함 된 열을 입력으로 사용 하 고 열에 필터를 적용 합니다. 필터링된 출력을 포함하는 새 동적 배열 열을 출력합니다.  

## <a name="syntax"></a>구문

`series_iir(`*x* `,` *b* `,` *a*`)`

## <a name="arguments"></a>인수

* *x*: 숫자 값의 배열인 동적 배열 셀입니다. 일반적으로 [series](make-seriesoperator.md) 또는 [make_list](makelist-aggfunction.md) 연산자의 결과 출력입니다.
* *b*: 숫자 값의 동적 배열로 저장 된 필터의 분자 계수를 포함 하는 상수 식입니다.
* *a*: *b*와 같은 상수 식입니다. 필터의 분모 계수를 포함합니다.

> [!IMPORTANT]
> `a`0으로 나누기를 방지 하려면의 첫 번째 요소 (즉, `a[0]` )가 0 되어서는 안됩니다. [아래 수식을](#the-filters-recursive-formula)참조 하십시오.

## <a name="the-filters-recursive-formula"></a>필터의 재귀 수식입니다.

* 입력 배열 X를 고려 하 고 배열 a와 b의 길이를 각각 n_a 및 n_b 계수 합니다. 출력 배열 Y를 생성 하는 필터의 전송 함수는 다음에 의해 정의 됩니다.

<div align="center">
Y<sub>i</sub> = a<sub>0</sub><sup>-1</sup>(b<sub>0</sub>x<sub>i</sub> 
 + b<sub>1</sub>x i<sub>-1</sub> + ... + b<sub>n<sub>b</sub>-1</sub>x i<sub>-n<sub>b</sub>-1</sub> 
 - a<sub>1</sub>y i-<sub>1</sub>-a<sub>2</sub>Y<sub>i-2</sub> - ...-a<sub>n-1<sub>a</sub></sub>y<sub>i-n-1<sub>a</sub></sub>)
</div>

## <a name="example"></a>예제

누적 합계를 계산 합니다. 계수 *a*= [1,-1] 및 *b*= [1] 인 iir 필터를 사용 합니다.  

<!-- csl: https://help.kusto.windows.net:443/Samples -->
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

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let vector_sum=(x:dynamic)
{
  let y=array_length(x) - 1;
  toreal(series_iir(x, dynamic([1]), dynamic([1, -1]))[y])
};
print d=dynamic([0, 1, 2, 3, 4])
| extend dd=vector_sum(d)
```

|일            |dd  |
|-------------|----|
|`[0,1,2,3,4]`|`10`|
