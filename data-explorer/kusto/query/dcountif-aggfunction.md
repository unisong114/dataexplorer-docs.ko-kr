---
title: dcountif () (집계 함수)-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 dcountif () (집계 함수)에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: c14c4d9e0512d4bb054bb3df39acb4828be5a5bd
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92247544"
---
# <a name="dcountif-aggregation-function"></a>dcountif () (집계 함수)

*조건자* 가로 계산 되는 *Expr* 행의 고유 값 수에 대 한 예상 값을 반환 합니다 `true` . 

* [요약](summarizeoperator.md)내의 집계 컨텍스트에서만 사용할 수 있습니다.

[예측 정확도](dcount-aggfunction.md#estimation-accuracy)에 대해 읽어 보십시오.

## <a name="syntax"></a>구문

`dcountif(` *Expr*, *Predicate*, [ `,` *정확도*]를 요약 합니다.`)`

## <a name="arguments"></a>인수

* *Expr*: 집계 계산에 사용 되는 식입니다.
* *Predicate*: 행을 필터링 하는 데 사용 되는 식입니다.
* *정확도*를 지정한 경우 속도와 정확도 간의 균형을 제어합니다.
    * `0` = 정확성은 가장 떨어지지만 가장 빠른 계산입니다. 1.6% 오류
    * `1` = 기본값은 정확도와 계산 시간을 조정 합니다. 0.8% 오류가 발생 했습니다.
    * `2` = 정확 하 고 느리게 계산; 0.4% 오류가 발생 했습니다.
    * `3` = 추가 정확도 및 저속 계산; 0.28% 오류가 발생 했습니다.
    * `4` = 매우 정확 하 고 속도가 가장 느린 계산; 0.2% 오류가 발생 했습니다.
    
## <a name="returns"></a>반환

그룹에서 *조건자* 가로 계산 되는 *Expr* 행의 고유 값 수를 예상 하 여 반환 합니다 `true` . 

## <a name="example"></a>예제

```kusto
PageViewLog | summarize countries=dcountif(country, country startswith "United") by continent
```

**팁: 오프셋 오류**

`dcountif()`모든 행이 전달 되거나 행이 모두 전달 되지 않는에 지 사례에서 일회용 오류가 발생할 수 있습니다. `Predicate`