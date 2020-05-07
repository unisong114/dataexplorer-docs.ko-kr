---
title: row_window_session ()-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 row_window_session ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 51bc5e26cdc2d002b29ec435a68421ba8768a7a4
ms.sourcegitcommit: 9fe6ee7db15a5cc92150d3eac0ee175f538953d2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2020
ms.locfileid: "82907187"
---
# <a name="row_window_session"></a>row_window_session()

`row_window_session()`[serialize 된 행 집합](./windowsfunctions.md#serialized-row-set)에서 열의 세션 시작 값을 계산 합니다.

**구문**

`row_window_session` `(` *`Expr`* `,` *`MaxDistanceFromFirst`* `,` *`MaxDistanceBetweenNeighbors`* [`,` *`Restart`*] `)`

* *`Expr`* 값이 세션에서 함께 그룹화 되는 식입니다.
  Null 값은 null 값을 생성 하 고 다음 값은 새 세션을 시작 합니다.
  *`Expr`* 형식의 `datetime`스칼라 식 이어야 합니다.

* *`MaxDistanceFromFirst`* 새 세션을 시작 하기 위한 한 가지 조건을 설정 합니다 .의 *`Expr`* 현재 값과 세션의 시작 부분 *`Expr`* 에 있는 값 사이의 최대 거리입니다.
  형식의 `timespan`스칼라 상수입니다.

* *`MaxDistanceBetweenNeighbors`* 새 세션을 시작 하기 위한 두 번째 조건을 설정 합니다. 한 값에서 다음 값 *`Expr`* 으로의 최대 거리입니다.
  형식의 `timespan`스칼라 상수입니다.

* *Restart* 는 형식의 `boolean`선택적 스칼라 식입니다. 지정 된 경우로 `true` 평가 되는 모든 값은 세션을 즉시 다시 시작 합니다.

**반환**

함수는 각 세션이 시작 될 때 값을 반환 합니다.

**참고 사항**

함수에는 다음과 같은 개념적 계산 모델이 있습니다.

1. 값 *`Expr`* 의 입력 시퀀스를 순서 대로 이동 합니다.

1. 모든 값에 대해 새 세션을 설정 하는지 확인 합니다.

1. 새 세션을 설정 하는 경우의 *`Expr`* 값을 내보냅니다. 그렇지 않으면의 *`Expr`* 이전 값을 내보냅니다.

다음 조건 중 하나에 해당 하는 값이 새 세션을 나타내는지 여부를 결정 하는 조건입니다.

* 이전 세션 값이 없거나 이전 세션 값이 null 인 경우

* 의 *`Expr`* 값이와 같거나 이전 세션 값을 더한 *`MaxDistanceFromFirst`* 값 보다 큰 경우

* 의 *`Expr`* 값이와 같거나 이전 값을 *`Expr`* 더한 *`MaxDistanceBetweenNeighbors`* 값 보다 큰 경우

**예**

다음 예에서는 시퀀스를 식별 하는 `ID` 열과 `Timestamp` 각 레코드가 발생 한 시간을 제공 하는 열이 있는 테이블에 대 한 세션 시작 값을 계산 하는 방법을 보여 줍니다. 이 예제에서 세션은 1 시간을 초과할 수 없으며 레코드가 5 분 미만인 경우 계속 됩니다.

```kusto
datatable (ID:string, Timestamp:datetime) [
    // ...
]
| sort by ID asc, Timestamp asc
| extend SessionStarted = row_window_session(Timestamp, 1h, 5m, ID != prev(ID))
```