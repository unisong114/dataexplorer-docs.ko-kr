---
title: series_pearson_correlation ()-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 series_pearson_correlation ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/31/2019
ms.openlocfilehash: aa75e3cb2f2aefbc50c148486cb687841408e1d4
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92245956"
---
# <a name="series_pearson_correlation"></a>series_pearson_correlation()

두 숫자 계열 입력의 피어슨 상관 계수를 계산 합니다.

참조: [피어슨 상관 계수](https://en.wikipedia.org/wiki/Pearson_correlation_coefficient)

## <a name="syntax"></a>구문

`series_pearson_correlation(`*Series1* `,` *Series2*`)`

## <a name="arguments"></a>인수

* *Series1, Series2*: 상관 계수를 계산 하는 데 사용할 숫자 배열을 입력 합니다. 모든 인수는 길이가 같은 동적 배열 이어야 합니다. 

## <a name="returns"></a>반환

두 입력 간의 계산 된 피어슨 상관 계수입니다. 숫자가 아닌 요소 또는 존재 하지 않는 요소 (크기가 다른 배열)는 결과를 생성 `null` 합니다.

## <a name="example"></a>예제

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
range s1 from 1 to 5 step 1 | extend s2 = 2*s1 // Perfect correlation
| summarize s1 = make_list(s1), s2 = make_list(s2)
| extend correlation_coefficient = series_pearson_correlation(s1,s2)
```

|s1|s2|correlation_coefficient|
|---|---|---|
|[1, 2, 3, 4, 5]|[2, 4, 6, 8, 10]|1|
