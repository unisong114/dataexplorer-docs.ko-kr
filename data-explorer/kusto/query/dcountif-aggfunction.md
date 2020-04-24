---
title: dcountif() (집계 함수) - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 dcountif(집계 함수)에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 1cc3c17474db835f381cac32d6107acb312c3d11
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81516051"
---
# <a name="dcountif-aggregation-function"></a>카운트@로그(@d)]

*조건어가* 평가하는 행의 `true` *Expr* 의 고유 값 수에 대한 추정치를 반환합니다. 

* 요약 내부 의 집계 컨텍스트에서만 사용할 수 [있습니다.](summarizeoperator.md)

[추정 정확도에](dcount-aggfunction.md#estimation-accuracy)대해 읽어보십시오.

**구문**

`dcountif(`요약 *Expr*, *조건어*`,` , [ *정확도*]`)`

**인수**

* *Expr*: 집계 계산에 사용되는 표현식입니다.
* *조건자*: 행을 필터링하는 데 사용되는 표현식입니다.
* *정확도*를 지정한 경우 속도와 정확도 간의 균형을 제어합니다.
    * `0` = 정확성은 가장 떨어지지만 가장 빠른 계산입니다. 1.6% 오류
    * `1`= 정확도와 계산 시간의 균형을 맞추는 기본값; 약 0.8% 오류.
    * `2`= 정확하고 느린 계산; 약 0.4% 오류.
    * `3`= 여분의 정확하고 느린 계산; 약 0.28% 오류.
    * `4`= 매우 정확하고 느린 계산; 약 0.2% 오류.
    
**반환**

*조건어가* 그룹에서 평가하는 행의 `true` *Expr의* 고유 값 수에 대한 추정치를 반환합니다. 

**예제**

```kusto
PageViewLog | summarize countries=dcountif(country, country startswith "United") by continent
```

**팁: 오프셋 오류**

`dcountif()`모든 행이 전달되거나 `Predicate` 행이 전달되지 않는 에지 케이스에서 일회성 오류가 발생할 수 있습니다.