---
title: arg_max () (집계 함수)-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기에서 arg_max () (집계 함수)에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 1ce8bb0635743fd6692cda3b707bbb7d88e07af9
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349706"
---
# <a name="arg_max-aggregation-function"></a>arg_max () (집계 함수)

그룹에서 *ExprToMaximize*를 최대화 하는 행을 찾은 다음 *ExprToReturn* 값을 반환 하거나 `*` 전체 행을 반환 합니다.

* [요약](summarizeoperator.md) 내의 집계 컨텍스트에서만 사용할 수 있습니다.

## <a name="syntax"></a>Syntax

`summarize`[ `(` *NameExprToMaximize* `,` *NameExprToReturn* [ `,` ...] `)=` ] `arg_max` `(` *ExprToMaximize*, `*`  |  *ExprToReturn* [ `,` ...]`)`

## <a name="arguments"></a>인수

* *ExprToMaximize*: 집계 계산에 사용 되는 식입니다. 
* *ExprToReturn*: *ExprToMaximize* 가 maximum 인 경우 값을 반환 하는 데 사용 되는 식입니다. 반환 되는 식은 입력 테이블의 모든 열을 반환 하는 와일드 카드 (*) 일 수 있습니다.
* *NameExprToMaximize*: *ExprToMaximize*을 나타내는 결과 열의 선택적 이름입니다.
* *NameExprToReturn*: *ExprToReturn*을 나타내는 결과 열의 추가 선택적 이름입니다.

## <a name="returns"></a>반환

그룹에서 *ExprToMaximize*를 최대화 하는 행을 찾은 다음 *ExprToReturn* 값을 반환 하거나 `*` 전체 행을 반환 합니다.

## <a name="examples"></a>예제

[Arg_min ()](arg-min-aggfunction.md) 집계 함수에 대 한 예를 참조 하세요.