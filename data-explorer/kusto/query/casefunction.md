---
title: case ()-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 사례 ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 087ff2fc38f3b72e4abdbb86ce4b7ac98a5569e6
ms.sourcegitcommit: d0f8d71261f8f01e7676abc77283f87fc450c7b1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/06/2020
ms.locfileid: "91765364"
---
# <a name="case"></a>case()

조건자 목록을 평가 하 고 조건자가 충족 된 첫 번째 결과 식을 반환 합니다.

조건자가 반환 `true` 되지 않으면 마지막 식 ()의 결과가 `else` 반환 됩니다.
모든 홀수 인수 (개수는 1부터 시작)는 값으로 계산 되는 식 이어야 합니다  `boolean` .
모든 짝수 인수 ( `then` s)와 마지막 인수 ( `else` )는 동일한 형식 이어야 합니다.

## <a name="syntax"></a>구문

`case(`*predicate_1*, *then_1*, *predicate_2*, *then_2*, *predicate_3*, *then_3*, *기타*`)`

## <a name="arguments"></a>인수

* *predicate_i*: 값으로 계산 되는 식 `boolean` 입니다.
* *then_i*: *predicate_i* 가로 계산 되는 첫 번째 조건자 인 경우 계산 된 식과 해당 값이 함수에서 반환 됩니다 `true` .
* *else*: 계산 되는 식과 해당 값이 *predicate_i* 모두로 계산 되지 않는 경우 함수에서 `true` 반환 됩니다.

## <a name="returns"></a>반환

*Predicate_i* 가로 계산 되는 첫 번째 *then_i* 의 값 `true` 또는 조건자가 모두 충족 되지 않는 경우 *else* 의 값입니다.

## <a name="example"></a>예제: 

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
range Size from 1 to 15 step 2
| extend bucket = case(Size <= 3, "Small", 
                       Size <= 10, "Medium", 
                       "Large")
```

|크기|bucket|
|---|---|
|1|소형|
|3|소형|
|5|중간|
|7|중간|
|9|중간|
|11|대형|
|13|대형|
|15|대형|
