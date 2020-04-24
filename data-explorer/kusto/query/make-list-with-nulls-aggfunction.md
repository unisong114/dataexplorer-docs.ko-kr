---
title: make_list_with_nulls() (집계 기능) - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 make_list_with_nulls(집계 함수)에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/09/2020
ms.openlocfilehash: 4b039008c5969cf02187d69a3486b09e04ec41ae
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81512872"
---
# <a name="make_list_with_nulls-aggregation-function"></a>make_list_with_nulls() (집계 함수)

null `dynamic` 값을 포함하여 그룹의 *Expr* 모든 값의 JSON(JSON) 배열을 반환합니다.

* 요약 내부 집계의 컨텍스트에서만 사용할 수 [있습니다.](summarizeoperator.md)

**구문**

`summarize``make_list_with_nulls(` *예시*`)`

**인수**

* *Expr*: 집계 계산에 사용되는 표현식입니다.

**반환**

null `dynamic` 값을 포함하여 그룹의 *Expr* 모든 값의 JSON(JSON) 배열을 반환합니다.
연산자의 `summarize` 입력이 정렬되지 않으면 결과 배열의 요소 순서가 정의되지 않습니다.
`summarize` 연산자의 입력이 정렬되면 결과 배열의 요소 순서가 입력의 입력을 추적합니다.

> [!TIP]
> 연산자를 [`mv-apply`](./mv-applyoperator.md) 사용하여 일부 키로 정렬된 목록을 만듭니다. [여기](./mv-applyoperator.md#using-mv-apply-operator-to-sort-the-output-of-makelist-aggregate-by-some-key)에서 예제를 참조합니다.
