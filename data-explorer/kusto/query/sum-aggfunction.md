---
title: sum() (집계 함수) - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기에서 sum() (집계 함수)에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: b729d9be8aba9683a053ef80acb3936c0c64d6a8
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81506684"
---
# <a name="sum-aggregation-function"></a>sum() (집계 함수)

그룹 전체의 *Expr* 합계를 계산합니다. 

* 요약 내부 집계의 컨텍스트에서만 사용할 수 [있습니다.](summarizeoperator.md)

**구문**

`sum(` *예시* 요약`)`

**인수**

* *Expr*: 집계 계산에 사용되는 표현식입니다. 

**반환**

그룹 전체의 *Expr* 합계 값입니다.
 