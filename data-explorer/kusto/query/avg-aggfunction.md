---
title: avg() (집계 함수) - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기에서 avg(집계 함수)에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 09/26/2019
ms.openlocfilehash: aadc756bdf4c6cab805f58a8a600815cf29680f7
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81518346"
---
# <a name="avg-aggregation-function"></a>평균() (집계 함수)

그룹 전체의 *Expr* 평균을 계산합니다. 

* 요약 내부 집계의 컨텍스트에서만 사용할 수 [있습니다.](summarizeoperator.md)

**구문**

`avg(` *예시* 요약`)`

**인수**

* *Expr*: 집계 계산에 사용되는 표현식입니다. 값이 `null` 있는 레코드는 무시되고 계산에 포함되지 않습니다.

**반환**

그룹 전체의 *Expr* 평균 값입니다.
 