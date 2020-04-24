---
title: count() (집계 함수) - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 개수(집계 함수)에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 01/23/2020
ms.openlocfilehash: 59b898d44507d844db1f714ef15effa004e5546f
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81517003"
---
# <a name="count-aggregation-function"></a>count() (집계 함수)

요약 그룹당 레코드 수를 반환합니다(또는 그룹화 없이 요약이 수행된 경우 총 레코드).

* 요약 내부 집계의 컨텍스트에서만 사용할 수 [있습니다.](summarizeoperator.md)
* [countif](countif-aggfunction.md) 집계 함수를 사용하여 일부 조건자 반환되는 레코드만 `true`계산합니다.

**구문**

요약`count()`

**반환**

요약 그룹당 레코드 수를 반환합니다(또는 그룹화 없이 요약이 수행된 경우 총 레코드).