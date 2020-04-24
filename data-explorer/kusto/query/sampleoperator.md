---
title: 샘플 연산자 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 샘플 연산자에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/18/2020
ms.openlocfilehash: 757830bde0c56ac727d5240c01ca4768eab28877
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81510033"
---
# <a name="sample-operator"></a>sample 연산자

입력 테이블에서 지정된 수의 임의 행까지 반환합니다.

```kusto
T | sample 5
```

**구문**

_T_ `| sample` _번호Ofrows_

**인수**

- _NumberOfRows_: 반환할 _T행의_ 수입니다. 숫자 식을 지정할 수 있습니다.

**참고 사항**

- `sample`값의 균일한 분포가 아닌 속도에 맞게 준비되어 있습니다. 구체적으로, 서로 다른 크기의 2개의 데이터 세트를 결합하는 연산자(예: `union` 또는 `join` 연산자)를 사용하는 경우 '공정한' 결과를 생성하지 않는다는 의미입니다. 테이블 참조 및 `sample` 필터 바로 이후에 사용하는 것이 좋습니다.

- `sample`은 결정적이지 않은 연산자이며 쿼리 중에 평가될 때마다 다른 결과 집합을 반환합니다. 예를 들어 다음 쿼리에서는 동일한 행을 두 번 반환할 것으로 예상되는 경우에도 두 개의 서로 다른 행을 생성합니다.

```kusto
let _data = range x from 1 to 100 step 1;
let _sample = _data | sample 1;
union (_sample), (_sample)
```

| x   |
| --- |
| 83  |
| 3   |

위의 `_sample` 예제에서 한 번 계산되도록 하기 위해 [materialize()](./materializefunction.md) 함수를 사용할 수 있습니다.

```kusto
let _data = range x from 1 to 100 step 1;
let _sample = materialize(_data | sample 1);
union (_sample), (_sample)
```

| x   |
| --- |
| 34  |
| 34  |

**팁**

- 지정된 수의 행이 아닌 특정 비율의 데이터를 샘플링하려는 경우

```kusto
StormEvents | where rand() < 0.1
```

- 행이 아닌 키를 샘플링하려는 경우(예: 10개의 ID를 샘플링하고 이러한 ID에 [`sample-distinct`](./sampledistinctoperator.md) 대한 모든 `in` 행을 가져옵니다) 연산자와 함께 사용할 수 있습니다.

**예**

```kusto
let sampleEpisodes = StormEvents | sample-distinct 10 of EpisodeId;
StormEvents
| where EpisodeId in (sampleEpisodes)
```