---
title: make_list_if () (집계 함수)-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기에서 make_list_if () (집계 함수)에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 362fd1b64ba156115515efa3db3214b0f86c5ad7
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92241379"
---
# <a name="make_list_if-aggregation-function"></a>make_list_if () (집계 함수)

`dynamic`그룹에서 *조건자* 가로 계산 되는 모든 *Expr* 값의 (JSON) 배열을 반환 `true` 합니다.

* [요약](summarizeoperator.md) 내의 집계 컨텍스트에서만 사용할 수 있습니다.

## <a name="syntax"></a>구문

`summarize``make_list_if(` *Expr*, *Predicate* [ `,` *MaxSize*]`)`

## <a name="arguments"></a>인수

* *Expr*: 집계 계산에 사용 되는 식입니다.
* *조건자*: `true` *Expr* 을 결과에 추가 하기 위해를 계산 해야 하는 조건자입니다.
* *MaxSize* 는 반환 되는 최대 요소 수에 대 한 선택적 정수 제한입니다 (기본값은 *1048576*). MaxSize 값은 1048576를 초과할 수 없습니다.

## <a name="returns"></a>반환

`dynamic`그룹에서 *조건자* 가로 계산 되는 모든 *Expr* 값의 (JSON) 배열을 반환 `true` 합니다.
연산자에 대 한 입력 `summarize` 이 정렬 되지 않은 경우 결과 배열의 요소 순서는 정의 되지 않습니다.
연산자에 대 한 입력 `summarize` 이 정렬 되는 경우 결과 배열의 요소 순서는 입력의를 추적 합니다.

## <a name="example"></a>예제

```kusto
let T = datatable(name:string, day_of_birth:long)
[
   "John", 9,
   "Paul", 18,
   "George", 25,
   "Ringo", 7
];
T
| summarize make_list_if(name, strlen(name) > 4)
```

|list_name|
|----|
|["George", "Ringo"]|

## <a name="see-also"></a>참조

[`make_list`](./makelist-aggfunction.md) 조건자 식 없이 동일 하 게 작동 하는 함수입니다.