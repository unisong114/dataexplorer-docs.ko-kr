---
title: tdigest_merge() (집계 기능) - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 tdigest_merge(집계 함수)에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 12/09/2019
ms.openlocfilehash: 0b7de916dd53c19a49301c8048e2d8867d1b1249
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81506395"
---
# <a name="tdigest_merge-aggregation-function"></a>tdigest_merge(집계 함수)

그룹 전체에서 tdigest 결과를 병합합니다. 

* 요약 내부 의 집계 컨텍스트에서만 사용할 수 [있습니다.](summarizeoperator.md)

기본 알고리즘(T-다이제스트)과 예상 오차에 대한 자세한 [내용은 여기를 참조하십시오.](percentiles-aggfunction.md#estimation-error-in-percentiles)

**구문**

`tdigest_merge(` *Expr을*`)`요약합니다.

`tdigest_merge(` *Expr* `)` - 별칭을 요약합니다.

**인수**

* *Expr*: 집계 계산에 사용되는 표현식입니다. 

**반환**

그룹 전체에서 *Expr의* 병합된 tdigest 값입니다.
 

**팁**

1) 기능 [ ]`percentile_tdigest()`(percentile-tdigestfunction.md)를 사용할 수 있습니다.

2) 동일한 그룹에 포함된 모든 다이제스트는 동일한 유형이어야 합니다.