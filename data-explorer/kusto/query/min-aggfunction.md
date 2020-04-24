---
title: min() (집계 함수) - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기에서 min(집계 함수)에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 07/24/2019
ms.openlocfilehash: ca50210c84b39f19e6717b27089313d0d116e21a
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81512413"
---
# <a name="min-aggregation-function"></a>최소() (집계 함수)

그룹 전체에서 최소 값을 반환합니다. 

* 요약 내부 집계의 컨텍스트에서만 사용할 수 [있습니다.](summarizeoperator.md)

**구문**

`summarize``min(` *예시*`)`

**인수**

* *Expr*: 집계 계산에 사용되는 표현식입니다. 

**반환**

그룹 전체의 *Expr* 최소 값입니다.
 
> [!TIP]
> 이렇게 하면 최소 또는 최대 값(예: 최고 또는 최저 가격)이 있습니다. 그러나 행의 다른 열 (예 : 최저 가격으로 공급자의 이름)을 원한다면 [arg_max](arg-max-aggfunction.md) 사용하거나 [arg_min](arg-min-aggfunction.md).