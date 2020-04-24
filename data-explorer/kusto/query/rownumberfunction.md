---
title: row_number() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 row_number()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: c8cb01ed098d24632154215ddf06dc2ab1d72695
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81510169"
---
# <a name="row_number"></a>row_number()

[직렬화된](./windowsfunctions.md#serialized-row-set)행 집합에서 현재 행의 인덱스를 반환합니다.
행 인덱스는 기본적으로 `1` 첫 번째 행에 대해 시작되며 `1` 각 추가 행에 대해 증가합니다.
선택적으로 행 인덱스는 `1`에서 와 다른 값으로 시작할 수 있습니다.
또한 행 인덱스는 제공된 일부 조건어에 따라 재설정될 수 있습니다.

**구문**

`row_number``(` [ 시작`,` *인덱스* [ 다시 *시작*]]`)`

* *startingIndex는* 시작(또는 `long` 다시 시작)할 행 인덱스의 값을 나타내는 형식의 상수 식입니다. 기본값은 `1`입니다.
* *다시 시작은* 번호 매기기를 `bool` *StartIndex* 값으로 다시 시작해야 하는 시기를 나타내는 형식의 선택적 인수입니다. 제공되지 않으면 기본값이 `false` 사용됩니다.

**반환**

함수는 현재 행의 행 인덱스를 형식의 `long`값으로 반환합니다.

**예**

다음 예제에서는 두 개의 열이 있는 테이블을`a`반환하고 `10` 첫 `1`번째 열()은`rn`아래부터 `1` 는 `10`숫자로, 두 번째 열()은 최대 다음의 숫자로 반환합니다.

```kusto
range a from 1 to 10 step 1
| sort by a desc
| extend rn=row_number()
```

다음 예제는 위의 경우와 유사하며 두`rn`번째 `7`열()만 다음에서 시작합니다.

```kusto
range a from 1 to 10 step 1
| sort by a desc
| extend rn=row_number(7)
```

마지막 예제에서는 데이터를 분할하고 각 파티션당 행수를 매기는 방법을 보여 주며, 여기서는 다음과 같은 `Airport`으로 데이터를 분할합니다.

```kusto
datatable (Airport:string, Airline:string, Departures:long)
[
  "TLV", "LH", 1,
  "TLV", "LY", 100,
  "SEA", "LH", 1,
  "SEA", "BA", 2,
  "SEA", "LY", 0
]
| sort by Airport asc, Departures desc
| extend Rank=row_number(1, prev(Airport) != Airport)
```

이 쿼리를 실행하면 다음과 같은 결과가 생성됩니다.

공항  | 항공사  | 출발  | Rank
---------|----------|-------------|------
SEA      | BA       | 2           | 1
SEA      | Lh       | 1           | 2
SEA      | LY       | 0           | 3
Tlv      | LY       | 100         | 1
Tlv      | Lh       | 1           | 2