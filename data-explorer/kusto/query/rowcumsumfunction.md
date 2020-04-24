---
title: row_cumsum() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 row_cumsum()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 92ebec75dcd7e44d59f964dc735e857f22f1ad00
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81510220"
---
# <a name="row_cumsum"></a>row_cumsum()

[직렬화된 행 집합에서](./windowsfunctions.md#serialized-row-set)열의 누적 합계를 계산합니다.

**구문**

`row_cumsum``(` *기간* `,` [ *다시 시작]*`)`

* *용어는* 합계할 값을 나타내는 표현식입니다.
  `decimal`식은 다음 형식 `int` `long` `real`중 하나의 스칼라여야 합니다. Null *기간* 값은 합계에 영향을 주지 않습니다.
* *다시 시작은* 누적 작업을 `bool` 다시 시작해야 하는 시기를 나타내는 형식의 선택적 인수입니다(0으로 다시 설정). 데이터의 파티션을 나타내는 데 사용할 수 있습니다. 아래 두 번째 예제를 참조하십시오.

**반환**

함수는 인수의 누적 합계를 반환합니다.

**예**

다음 예제에서는 처음 몇 개의 정수의 누적 합계를 계산하는 방법을 보여 주십습니다.

```kusto
datatable (a:long) [
    1, 2, 3, 4, 5, 6, 7, 8, 9, 10
]
| where a%2==0
| serialize cs=row_cumsum(a)
```

a    | cs
-----|-----
2    | 2
4    | 6
6    | 12
8    | 20
10   | 30

이 예제에서는 데이터가 분할될 때 누적 `salary`합계(여기, 의)를 계산하는 방법을 보여 주며 다음과 같습니다. `name`

```kusto
datatable (name:string, month:int, salary:long)
[
    "Alice", 1, 1000,
    "Bob",   1, 1000,
    "Alice", 2, 2000,
    "Bob",   2, 1950,
    "Alice", 3, 1400,
    "Bob",   3, 1450,
]
| order by name asc, month asc
| extend total=row_cumsum(salary, name != prev(name))
```

name   | month  | 급여  | total
-------|--------|---------|------
Alice  | 1      | 1000    | 1000
Alice  | 2      | 2000    | 3000
Alice  | 3      | 1400    | 4400
Bob    | 1      | 1000    | 1000
Bob    | 2      | 1950    | 2950
Bob    | 3      | 1450    | 4400