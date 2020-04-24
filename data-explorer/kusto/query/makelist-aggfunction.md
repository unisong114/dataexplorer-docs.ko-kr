---
title: make_list() (집계 기능) - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 make_list(집계 함수)에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 01/23/2020
ms.openlocfilehash: e46dbfac7b8c819f66d469c160452ec4dddfb769
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81512753"
---
# <a name="make_list-aggregation-function"></a>make_list() (집계 함수)

그룹에 있는 *Expr*의 모든 값의 `dynamic` (JSON) 배열을 반환합니다.

* 요약 내부 집계의 컨텍스트에서만 사용할 수 [있습니다.](summarizeoperator.md)

**구문**

`summarize``make_list(` *Expr* `,` [ *최대 크기*]`)`

**인수**

* *Expr*: 집계 계산에 사용되는 표현식입니다.
* *MaxSize는* 반환되는 최대 요소 수에 대한 선택적 정수 제한입니다(기본값은 *1048576).* MaxSize 값은 1048576을 초과할 수 없습니다.

> [!NOTE]
> 이 함수의 레거시 및 사용되지 `makelist()` 않는 변형: *MaxSize* = 128의 기본 제한이 있습니다.

**반환**

그룹에 있는 *Expr*의 모든 값의 `dynamic` (JSON) 배열을 반환합니다.
연산자의 `summarize` 입력이 정렬되지 않으면 결과 배열의 요소 순서가 정의되지 않습니다.
`summarize` 연산자의 입력이 정렬되면 결과 배열의 요소 순서가 입력의 입력을 추적합니다.

> [!TIP]
> 연산자를 [`mv-apply`](./mv-applyoperator.md) 사용하여 일부 키로 정렬된 목록을 만듭니다. [여기](./mv-applyoperator.md#using-mv-apply-operator-to-sort-the-output-of-makelist-aggregate-by-some-key)에서 예제를 참조합니다.

**참고 항목**

[`make_list_if`](./makelistif-aggfunction.md)연산자는 `make_list`조건자도 수락하는 경우를 제외하고는 과 유사합니다.