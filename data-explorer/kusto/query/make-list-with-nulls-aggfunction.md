---
title: make_list_with_nulls () (집계 함수)-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기에서 make_list_with_nulls () (집계 함수)에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/09/2020
ms.openlocfilehash: 1d4dafdab4c727b89838f18e13b016d771262f08
ms.sourcegitcommit: a4779e31a52d058b07b472870ecd2b8b8ae16e95
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/02/2020
ms.locfileid: "89366013"
---
# <a name="make_list_with_nulls-aggregation-function"></a>make_list_with_nulls () (집계 함수)

`dynamic`Null 값을 포함 하 여 그룹에 있는 모든 *Expr* 값의 (JSON) 배열을 반환 합니다.

* [요약](summarizeoperator.md) 내의 집계 컨텍스트에서만 사용할 수 있습니다.

## <a name="syntax"></a>구문

`summarize``make_list_with_nulls(` *Expr*`)`

## <a name="arguments"></a>인수

* *Expr*: 집계 계산에 사용 되는 식입니다.

## <a name="returns"></a>반환

`dynamic`Null 값을 포함 하 여 그룹에 있는 모든 *Expr* 값의 (JSON) 배열을 반환 합니다.
연산자에 대 한 입력 `summarize` 이 정렬 되지 않은 경우 결과 배열의 요소 순서는 정의 되지 않습니다.
연산자에 대 한 입력 `summarize` 이 정렬 되는 경우 결과 배열의 요소 순서는 입력의를 추적 합니다.

> [!TIP]
> 연산자를 사용 [`mv-apply`](./mv-applyoperator.md) 하 여 일부 키로 정렬 된 목록을 만듭니다. [여기](./mv-applyoperator.md#using-the-mv-apply-operator-to-sort-the-output-of-make_list-aggregate-by-some-key)에서 예제를 참조합니다.
