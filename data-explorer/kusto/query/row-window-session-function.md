---
title: row_window_session() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 row_window_session()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 87e89443bc85125e705bc180ea0cdb9e599c9c13
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81510254"
---
# <a name="row_window_session"></a>row_window_session()

`row_window_session()`[직렬화된 행 집합에서](./windowsfunctions.md#serialized-row-set)열의 세션 시작 값을 계산합니다.

**구문**

`row_window_session``(` *익스프레* `,` *맥스거리첫* `,` 번째 맥스`,` *거리사이이웃* [ 다시 *시작]*`)`

* *Expr은* 값의 값이 세션에서 함께 그룹화되는 표현식입니다.
  Null 값은 null 값을 생성하고 다음 값은 새 세션을 시작합니다.
  *Expr형식의* 스칼라 식이어야 `datetime`합니다.

* *MaxDistanceFromFirst는* 새 세션을 시작하기 위한 하나의 기준을 설정합니다: 현재 *Expr* 값과 세션 시작 시 *Expr* 값 사이의 최대 거리입니다.
  유형의 스칼라 `timespan`상수입니다.

* *MaxDistanceBetweenNeighbors는* 새 세션을 시작하기 위한 두 번째 기준을 설정합니다. *Expr*
  유형의 스칼라 `timespan`상수입니다.

* *다시 시작은* 형식의 `boolean`선택적 스칼라 식입니다. 지정하면 평가되는 `true` 모든 값이 즉시 세션을 다시 시작합니다.

**반환**

함수는 각 세션의 시작 부분에 값을 반환합니다.

**참고 사항**

함수에는 다음과 같은 개념 계산 모델이 있습니다.

1. 값 *Expr의* 입력 시퀀스를 순서대로 진행합니다.

2. 모든 값에 대해 새 세션을 설정하는지 확인합니다.

3. 새 세션을 설정하는 경우 *Expr*값을 내보엠피합니다. 그렇지 않으면 *Expr의*이전 값을 내보전합니다.

값이 새 세션을 나타내는지 여부를 결정하는 조건은 다음의 논리적 OR입니다.

* 이전 세션 값이 없거나 이전 세션 값이 null인 경우

* *Expr* 값이 이전 세션 값과 같거나 초과하는 경우 *MaxDistanceFromFirst*.

* *Expr* 값이 *Expr의* 이전 값과 *MaxDistanceBetweenNeighbors를*초과하거나 초과하는 경우.

**예**

다음 예제에서는 두 개의 열, 시퀀스를 식별하는 `ID` 열 및 각 레코드가 발생한 `Timestamp` 시간을 제공하는 열이 있는 테이블에 대한 세션 시작 값을 계산하는 방법을 보여 줍니다. 이 예제에서는 세션이 1시간을 초과할 수 없으며 레코드가 5분 미만인 한 계속됩니다.

```kusto
datatable (ID:string, Timestamp:datetime) [
    // ...
]
| sort by ID asc, Timestamp asc
| extend SessionStarted = row_window_session(Timestamp, 1h, 5m, ID != prev(ID))
```