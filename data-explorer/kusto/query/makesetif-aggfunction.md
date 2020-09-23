---
title: make_set_if () (집계 함수)-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기에서 make_set_if () (집계 함수)에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 262676e2ee40b619c4984a23c23818c493aad47a
ms.sourcegitcommit: 4e95f5beb060b5d29c1d7bb8683695fe73c9f7ea
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/23/2020
ms.locfileid: "91103048"
---
# <a name="make_set_if-aggregation-function"></a>make_set_if () (집계 함수)

`dynamic` *조건자* 가로 계산 되는 그룹에 사용 되는 고유 값 *Expr* 집합의 (JSON) 배열을 반환 `true` 합니다.

* [요약](summarizeoperator.md) 내의 집계 컨텍스트에서만 사용할 수 있습니다.

## <a name="syntax"></a>구문

`summarize``make_set_if(` *Expr*, *Predicate* [ `,` *MaxSize*]`)`

## <a name="arguments"></a>인수

* *Expr*: 집계 계산에 사용 되는 식입니다.
* *조건자*: `true` *Expr* 을 결과에 추가 하려면로 계산 해야 하는 조건자입니다.
* *MaxSize* 는 반환 되는 최대 요소 수에 대 한 선택적 정수 제한입니다 (기본값은 *1048576*). MaxSize 값은 1048576를 초과할 수 없습니다.

## <a name="returns"></a>반환

`dynamic` *조건자* 가로 계산 되는 그룹에 사용 되는 고유 값 *Expr* 집합의 (JSON) 배열을 반환 `true` 합니다.
배열의 정렬 순서가 정의 되어 있지 않습니다.

> [!TIP]
> 고유 값만 계산 하려면 [dcountif ()](dcountif-aggfunction.md) 를 사용 합니다.

## <a name="see-also"></a>참고 항목

[`make_set`](./makeset-aggfunction.md) 조건자 식 없이 동일 하 게 작동 하는 함수입니다.

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
| summarize make_set_if(name, strlen(name) > 4)
```

|set_name|
|----|
|["George", "Ringo"]|