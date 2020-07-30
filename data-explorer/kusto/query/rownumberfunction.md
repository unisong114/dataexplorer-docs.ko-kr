---
title: row_number ()-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기에서 row_number ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: ea51e6171b8a7683a0454d177dc729ed754b8896
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87351593"
---
# <a name="row_number"></a>row_number()

[Serialize 된 행 집합](./windowsfunctions.md#serialized-row-set)에서 현재 행의 인덱스를 반환 합니다.
행 인덱스는 `1` 첫 번째 행에 대해 기본적으로 시작 되며, `1` 각 추가 행에 대해에서 증가 합니다.
필요에 따라 행 인덱스는와 다른 값으로 시작 될 수 있습니다 `1` .
또한 일부 제공 된 조건자에 따라 행 인덱스를 다시 설정할 수 있습니다.

## <a name="syntax"></a>Syntax

`row_number``(`[*StartingIndex* [ `,` *다시 시작*]]`)`

* *StartingIndex* 은 `long` 시작할 행 인덱스의 값 (또는로 다시 시작)을 나타내는 형식의 상수 식입니다. 기본값은 `1`입니다.
* *다시 시작* 은 `bool` *StartingIndex* 값으로 번호 매기기를 다시 시작할지 여부를 나타내는 형식의 선택적 인수입니다. 이 값을 지정 하지 않으면 기본값인 `false` 가 사용 됩니다.

## <a name="returns"></a>반환

함수는 현재 행의 행 인덱스를 형식의 값으로 반환 합니다 `long` .

## <a name="examples"></a>예제

다음 예에서는 두 개의 열 ()이 포함 된 테이블을 반환 합니다. 첫 번째 열 ( `a` )의 값은 아래에서 사이 `10` `1` 이 고, 두 번째 열 ( `rn` )의 값은 `1` 최대입니다 `10` .

```kusto
range a from 1 to 10 step 1
| sort by a desc
| extend rn=row_number()
```

다음 예는 위의 경우와 유사 하며 두 번째 열 ()은 `rn` 에서 시작 됩니다 `7` .

```kusto
range a from 1 to 10 step 1
| sort by a desc
| extend rn=row_number(7)
```

마지막 예에서는 하나의 데이터를 분할 하 고 각 파티션당 행 수를 표시 하는 방법을 보여 줍니다. 여기서는 다음을 기준으로 데이터를 분할 합니다 `Airport` .

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

이 쿼리를 실행 하면 다음과 같은 결과가 생성 됩니다.

예  | 항공사  | 출발  | 순위
---------|----------|-------------|------
SEA      | BA       | 2           | 1
SEA      | LH       | 1           | 2
SEA      | LY       | 0           | 3
받지      | LY       | 100         | 1
받지      | LH       | 1           | 2