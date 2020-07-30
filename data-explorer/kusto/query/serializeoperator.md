---
title: 직렬화 연산자-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 직렬화 연산자에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 92ee54ce675c2e8396d842fdf029f2d3f3d5380b
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87345677"
---
# <a name="serialize-operator"></a>serialize 연산자

입력 행 집합의 순서가 창 함수에 사용 하기에 안전 하다 고 표시 합니다.

연산자에는 선언적 의미가 있습니다. 입력 행 집합을 serialize 된 (정렬 됨)로 표시 하 여 [창 함수](./windowsfunctions.md) 를 적용할 수 있도록 합니다.

```kusto
T | serialize rn=row_number()
```

## <a name="syntax"></a>Syntax

`serialize`[*Name1* `=` *Expr1* [ `,` *Name2* `=` *Expr2*] ...]

* *이름* / *Expr* 쌍은 [확장 연산자](./extendoperator.md)의 해당 쌍과 비슷합니다.

## <a name="example"></a>예제

```kusto
Traces
| where ActivityId == "479671d99b7b"
| serialize

Traces
| where ActivityId == "479671d99b7b"
| serialize rn = row_number()
```

다음 연산자의 출력 행 집합은 serialize 된 것으로 표시 됩니다.

[범위](./rangeoperator.md), [정렬](./sortoperator.md), [순서](./orderoperator.md), [위쪽](./topoperator.md), [위쪽-hitters](./tophittersoperator.md), [getschema](./getschemaoperator.md).

다음 연산자의 출력 행 집합이 직렬화 되지 않은 것으로 표시 되었습니다.

[샘플](./sampleoperator.md), [샘플 고유](./sampledistinctoperator.md), [고유](./distinctoperator.md), [조인](./joinoperator.md), [상위 중첩](./topnestedoperator.md), [개수](./countoperator.md), [요약](./summarizeoperator.md), [패싯](./facetoperator.md), [mv-확장](./mvexpandoperator.md), [평가](./evaluateoperator.md), [감소](./reduceoperator.md), [시리즈 시리즈](./make-seriesoperator.md)

다른 모든 연산자는 serialization 속성을 유지 합니다. 입력 행 집합이 serialize 되 면 출력 행 집합도 serialize 됩니다.
