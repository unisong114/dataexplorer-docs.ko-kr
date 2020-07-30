---
title: row_cumsum ()-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기에서 row_cumsum ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 83dc48589fce7332c8e24d1e5a47c75a6cfca608
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87345728"
---
# <a name="row_cumsum"></a>row_cumsum()

[직렬화 된 행 집합](./windowsfunctions.md#serialized-row-set)에 있는 열의 누적 합계를 계산 합니다.

## <a name="syntax"></a>Syntax

`row_cumsum``(` *용어* [ `,` *다시 시작*]`)`

* *Term* 은 합계를 계산할 값을 나타내는 식입니다.
  식은 `decimal` ,, `int` `long` 또는 형식 중 하나의 스칼라 여야 `real` 합니다. Null *용어* 값은 합계에 영향을 주지 않습니다.
* *Restart* 는 `bool` 누적 작업을 다시 시작 해야 하는 경우 (다시 0으로 설정 됨)를 나타내는 형식의 선택적 인수입니다. 데이터의 파티션을 나타내는 데 사용할 수 있습니다. 아래의 두 번째 예제를 참조 하세요.

## <a name="returns"></a>반환

함수는 인수의 누적 합계를 반환 합니다.

## <a name="examples"></a>예제

다음 예제에서는 처음 몇 개의 짝수 정수의 누적 합계를 계산 하는 방법을 보여 줍니다.

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

이 예에서는 `salary` 데이터를 분할할 때 (여기서는) 누적 합계를 계산 하는 방법을 보여 줍니다 `name` .

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

name   | month  | 직위가  | total
-------|--------|---------|------
Alice  | 1      | 1000    | 1000
Alice  | 2      | 2000    | 3000
Alice  | 3      | 1400    | 4400
Bob    | 1      | 1000    | 1000
Bob    | 2      | 1950    | 2950
Bob    | 3      | 1450    | 4400