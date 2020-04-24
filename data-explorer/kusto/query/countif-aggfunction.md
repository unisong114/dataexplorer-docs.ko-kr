---
title: 카운트리프() (집계 함수) - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 countif(집계 함수)에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 03b6f1cb959706463a73d8aa18a11144e2123492
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81516986"
---
# <a name="countif-aggregation-function"></a>카운트리프() (집계 함수)

*Predicate*가 `true`(으)로 계산되는 행 수를 반환합니다.

* 요약 내부 집계의 컨텍스트에서만 사용할 수 [있습니다.](summarizeoperator.md)

또한 - 조건자 식없이 행을 계산하는 [count()](count-aggfunction.md) 함수를 참조하십시오.

**구문**

`countif(` *조건자* 요약`)`

**인수**

* *조건자*: 집계 계산에 사용되는 표현식입니다. 

**반환**

*Predicate*가 `true`(으)로 계산되는 행 수를 반환합니다.

> [!TIP]
> `where filter | summarize count()` 대신 `summarize countif(filter)` 사용