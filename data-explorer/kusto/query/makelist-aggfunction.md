---
title: make_list () (집계 함수)-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기에서 make_list () (집계 함수)에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 01/23/2020
ms.openlocfilehash: fed2616f5fbd32b1c80f936d5689261467a9dc5e
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83224837"
---
# <a name="make_list-aggregation-function"></a>make_list () (집계 함수)

그룹에 있는 *Expr*의 모든 값의 `dynamic` (JSON) 배열을 반환합니다.

* [요약](summarizeoperator.md) 내의 집계 컨텍스트에서만 사용할 수 있습니다.

**구문**

`summarize``make_list(` *Expr* [ `,` *MaxSize*]`)`

**인수**

* *Expr*: 집계 계산에 사용 되는 식입니다.
* *MaxSize* 는 반환 되는 최대 요소 수에 대 한 선택적 정수 제한입니다 (기본값은 *1048576*). MaxSize 값은 1048576를 초과할 수 없습니다.

> [!NOTE]
> 이 함수의 기존 변형 및 사용 되지 않는 변형: `makelist()` 기본 제한은 *MaxSize* = 128입니다.

**반환**

그룹에 있는 *Expr*의 모든 값의 `dynamic` (JSON) 배열을 반환합니다.
연산자에 대 한 입력 `summarize` 이 정렬 되지 않은 경우 결과 배열의 요소 순서는 정의 되지 않습니다.
연산자에 대 한 입력 `summarize` 이 정렬 되는 경우 결과 배열의 요소 순서는 입력의를 추적 합니다.

> [!TIP]
> 연산자를 사용 [`mv-apply`](./mv-applyoperator.md) 하 여 일부 키로 정렬 된 목록을 만듭니다. [여기](./mv-applyoperator.md#using-the-mv-apply-operator-to-sort-the-output-of-makelist-aggregate-by-some-key)에서 예제를 참조합니다.

**참고 항목**

[`make_list_if`](./makelistif-aggfunction.md)연산자는 조건자를 `make_list` 수락 한다는 점을 제외 하 고와 비슷합니다.