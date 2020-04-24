---
title: arg_max() (집계 기능) - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 arg_max() (집계 함수)에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 73953a17b1819081c8458d5dbde3fa6d55c8866e
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81518958"
---
# <a name="arg_max-aggregation-function"></a>arg_max() (집계 함수)

*ExprToMaximize를*최대화하는 그룹에서 행을 찾아 *ExprToReturn* 값을 반환합니다(또는 `*` 전체 행을 반환).

* 요약 내부 집계의 컨텍스트에서만 사용할 수 [있습니다.](summarizeoperator.md)

**구문**

`summarize`[`(`*네임엑스프토최대화* `,` *네임엑스프르토리턴* [...]`,` `)=`] `arg_max` `,` `*`  |  *ExprToReturn* *ExprToMaximize*Exprto최대화 , ExprtoReturn [...] `(``)`

**인수**

* *ExprToMaximize*: 집계 계산에 사용되는 표현식입니다. 
* *ExprToReturn*: *ExprToMaximize가* 최대일 때 값을 반환하는 데 사용되는 표현식입니다. 반환할 표현식은 입력 테이블의 모든 열을 반환하는 와일드카드(*)일 수 있습니다.
* *NameExprToMaximize*: *ExprToMaximize를*나타내는 결과 열에 대한 선택적 이름입니다.
* *NameExprToReturn*: *ExprToReturn을*나타내는 결과 열에 대한 추가 선택적 이름 .

**반환**

*ExprToMaximize를*최대화하는 그룹에서 행을 찾아 *ExprToReturn* 값을 반환합니다(또는 `*` 전체 행을 반환).

**예**

[arg_min()](arg-min-aggfunction.md) 집계 함수에 대한 예제 를 참조하십시오.