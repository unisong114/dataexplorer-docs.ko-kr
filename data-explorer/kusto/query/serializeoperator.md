---
title: 연산자 직렬화 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 연산자 직렬화에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 5402d1e1fcceb42f02643bf24918ed07beddaed7
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81509115"
---
# <a name="serialize-operator"></a>serialize 연산자

입력 행 집합의 순서가 창 함수 사용에 안전하다는 표시입니다.

연산자는 선언적 의미를 가지며 입력 행 집합을 직렬화된(정렬된) 것으로 표시하여 [창 함수를](./windowsfunctions.md) 적용할 수 있습니다.

```kusto
T | serialize rn=row_number()
```

**구문**

`serialize`[*Name1* `=` *Expr1* `,` *[Name2* `=` *Expr2]...]*

* *이름*/*Expr* 쌍은 확장 [연산자의](./extendoperator.md)쌍과 유사합니다.

**예제**

```kusto
Traces
| where ActivityId == "479671d99b7b"
| serialize

Traces
| where ActivityId == "479671d99b7b"
| serialize rn = row_number()
```

다음 연산자의 출력 행 집합은 직렬화로 표시됩니다.

[범위,](./rangeoperator.md) [정렬,](./sortoperator.md) [순서,](./orderoperator.md) [상단,](./topoperator.md) [최고 타자,](./tophittersoperator.md) [getschema](./getschemaoperator.md).

다음 연산자의 출력 행 집합은 직렬화되지 않은 것으로 표시됩니다.

[샘플,](./sampleoperator.md) [샘플 구별](./sampledistinctoperator.md), [구별](./distinctoperator.md), [조인](./joinoperator.md), [최고 중첩](./topnestedoperator.md), [카운트](./countoperator.md), [요약](./summarizeoperator.md), [면,](./facetoperator.md) [mv 확장](./mvexpandoperator.md), [평가](./evaluateoperator.md) [,](./reduceoperator.md) [[ 메이크 시리즈]](./make-seriesoperator.md)

다른 모든 연산자는 직렬화 속성을 보존합니다(입력 행 집합이 직렬화된 경우 출력 행 집합도 마찬가지입니다).