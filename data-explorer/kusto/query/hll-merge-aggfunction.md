---
title: hll_merge() (집계 기능) - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 hll_merge(집계 함수)에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/15/2019
ms.openlocfilehash: 4700d5c87bf0f29f7bba86d56114a6a61092da94
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81514113"
---
# <a name="hll_merge-aggregation-function"></a>hll_merge() (집계 함수)

그룹 전체의 HLL 결과를 단일 HLL 값으로 병합합니다.

* 요약 내부 의 집계 컨텍스트에서만 사용할 수 [있습니다.](summarizeoperator.md)

[기본 알고리즘 *(H*yper*L*og*Og*og)과 추정 정확도에](dcount-aggfunction.md#estimation-accuracy)대해 읽어보십시오.

**구문**

`summarize``hll_merge(` *예시*`)`

**인수**

* *Expr*: 집계 계산에 사용되는 표현식입니다. 

**반환**

그룹 전체에서 *Expr의* 병합된 hll 값입니다.
 
**팁**

1) hll/hll 병합 집계 함수에서 dcount를 계산하는 함수 [dcount_hll](dcount-hllfunction.md)를 사용할 수 있습니다.