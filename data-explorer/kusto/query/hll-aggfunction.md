---
title: hll() (집계 함수) - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기에서 hll(집계 함수)에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 01/15/2020
ms.openlocfilehash: 52eac2984ed29bf8de21fb378a84b789015aa1c5
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81514130"
---
# <a name="hll-aggregation-function"></a>hll() (집계 함수)

그룹 전체의 [dcount의](dcount-aggfunction.md) 중간 결과를 계산합니다. 

* 요약 내부 의 집계 컨텍스트에서만 사용할 수 [있습니다.](summarizeoperator.md)

[기본 알고리즘 *(H*yper*L*og*Og*og)과 추정 정확도에](dcount-aggfunction.md#estimation-accuracy)대해 읽어보십시오.

**구문**

`summarize``hll(` *ExPR* `,` [ *정확도]*`)`

**인수**

* *Expr*: 집계 계산에 사용되는 표현식입니다. 
* *정확도*를 지정한 경우 속도와 정확도 간의 균형을 제어합니다.
    * `0` = 정확성은 가장 떨어지지만 가장 빠른 계산입니다. 1.6% 오류
    * `1`= 정확도와 계산 시간의 균형을 맞추는 기본값; 약 0.8% 오류.
    * `2`= 정확하고 느린 계산; 약 0.4% 오류.
    * `3`= 여분의 정확하고 느린 계산; 약 0.28% 오류.
    * `4`= 매우 정확하고 느린 계산; 약 0.2% 오류.
    
**반환**

그룹 전체의 *뚜렷한 Expr* 수의 중간 결과입니다.
 
**팁**

1) 집계 함수 [hll_merge](hll-merge-aggfunction.md) 사용하여 두 개 이상의 hll 중간 결과를 병합할 수 있습니다(hll 출력에서만 작동).

2) hll/hll_merge 집계 함수에서 dcount를 계산 하는 [dcount_hll](dcount-hllfunction.md) 함수를 사용할 수 있습니다.

**예**

```kusto
StormEvents
| summarize hll(DamageProperty) by bin(StartTime,10m)

```

|StartTime|hll_DamageProperty|
|---|---|
|2007-09-18 20:00:00.0000000|[[1024,14],[-5473486921211236216,-6230876016761372746,3953448761157777955,4246796580750024372],[]]|
|2007-09-20 21:50:00.0000000|[[1024,14],[4835649640695509390],[]]|
|2007-09-29 08:10:00.0000000|[[1024,14],[4246796580750024372],[]]|
|2007-12-30 16:00:00.0000000|[[1024,14],[4246796580750024372,-8936707700542868125],[]]|