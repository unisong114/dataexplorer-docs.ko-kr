---
title: countif () (집계 함수)-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 countif () (집계 함수)에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: a5b69b50c0cf4c07934d7900937675bf6338eab9
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348771"
---
# <a name="countif-aggregation-function"></a>countif () (집계 함수)

*Predicate*가 `true`(으)로 계산되는 행 수를 반환합니다.

* [요약](summarizeoperator.md) 내의 집계 컨텍스트에서만 사용할 수 있습니다.

조건자 식 없이 행을 계산 하는- [count ()](count-aggfunction.md) 함수를 참조 하세요.

## <a name="syntax"></a>Syntax

요약 `countif(` *조건자*`)`

## <a name="arguments"></a>인수

* *Predicate*: 집계 계산에 사용 되는 식입니다. 

## <a name="returns"></a>반환

*Predicate*가 `true`(으)로 계산되는 행 수를 반환합니다.

> [!TIP]
> `where filter | summarize count()` 대신 `summarize countif(filter)` 사용