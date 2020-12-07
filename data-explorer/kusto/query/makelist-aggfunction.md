---
title: make_list () (집계 함수)-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기에서 make_list () (집계 함수)에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 01/23/2020
ms.openlocfilehash: bd923724a391cfc1638057fec654a1bd74b78479
ms.sourcegitcommit: 1bdbfdc04c4eac405f3931059bbeee2dedd87004
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/27/2020
ms.locfileid: "96303373"
---
# <a name="make_list-aggregation-function"></a>make_list () (집계 함수)

그룹에 있는 *Expr* 의 모든 값의 `dynamic` (JSON) 배열을 반환합니다.

* [summarize](summarizeoperator.md) 내의 집계 컨텍스트에서만 사용할 수 있습니다.

## <a name="syntax"></a>Syntax

`summarize``make_list(` *Expr* [ `,` *MaxSize*]`)`

## <a name="arguments"></a>인수

* *Expr*: 집계 계산에 사용 되는 식입니다.
* *MaxSize* 는 반환 되는 최대 요소 수에 대 한 선택적 정수 제한입니다 (기본값은 *1048576*). MaxSize 값은 1048576를 초과할 수 없습니다.

> [!NOTE]
> `makelist()` 는 사용 되지 않는 레거시 버전의 `make_list` 함수입니다. 레거시 버전의 기본 제한은 *MaxSize* = 128입니다.

## <a name="returns"></a>반환

그룹에 있는 *Expr* 의 모든 값의 `dynamic` (JSON) 배열을 반환합니다.
연산자에 대 한 입력 `summarize` 이 정렬 되지 않은 경우 결과 배열의 요소 순서는 정의 되지 않습니다.
연산자에 대 한 입력 `summarize` 이 정렬 되는 경우 결과 배열의 요소 순서는 입력의를 추적 합니다.

> [!TIP]
> [`array_sort_asc()`](./arraysortascfunction.md)또는 함수를 사용 [`array_sort_desc()`](./arraysortdescfunction.md) 하 여 일부 키로 정렬 된 목록을 만듭니다.

## <a name="examples"></a>예제

### <a name="one-column"></a>한 열

가장 간단한 예는 단일 열에서 목록을 만드는 것입니다.

```kusto
let shapes = datatable (name: string, sideCount: int)
[
    "triangle", 3,
    "square", 4,
    "rectangle", 4,
    "pentagon", 5,
    "hexagon", 6,
    "heptagon", 7,
    "octogon", 8,
    "nonagon", 9,
    "decagon", 10
];
shapes
| summarize mylist = make_list(name)
```

|mylist|
|---|
|["삼각형", "square", "사각형", "오각형", "육각형", "heptagon", "octogon", "nonagon", "decagon"]|

### <a name="using-the-by-clause"></a>' By ' 절 사용

다음 쿼리에서는 절을 사용 하 여 그룹화 합니다 `by` .

```kusto
let shapes = datatable (name: string, sideCount: int)
[
    "triangle", 3,
    "square", 4,
    "rectangle", 4,
    "pentagon", 5,
    "hexagon", 6,
    "heptagon", 7,
    "octogon", 8,
    "nonagon", 9,
    "decagon", 10
];
shapes
| summarize mylist = make_list(name) by isEvenSideCount = sideCount % 2 == 0
```

|mylist|isEvenSideCount|
|---|---|
|false|["삼각형", "오각형", "heptagon", "nonagon"]|
|true|["square", "rectangle", "육각형", "octogon", "decagon"]|

### <a name="packing-a-dynamic-object"></a>동적 개체 압축

다음 쿼리에서 볼 수 있듯이 열에서 동적 개체를 [압축](./packfunction.md) 하 여 목록을 만들 수 있습니다.

```kusto
let shapes = datatable (name: string, sideCount: int)
[
    "triangle", 3,
    "square", 4,
    "rectangle", 4,
    "pentagon", 5,
    "hexagon", 6,
    "heptagon", 7,
    "octogon", 8,
    "nonagon", 9,
    "decagon", 10
];
shapes
| extend d = pack("name", name, "sideCount", sideCount)
| summarize mylist = make_list(d) by isEvenSideCount = sideCount % 2 == 0
```

|mylist|isEvenSideCount|
|---|---|
|false|[{"name": "삼각형", "sideCount": 3}, {"name": "오각형", "sideCount": 5}, {"name": "heptagon", "sideCount": 7}, {"name": "nonagon", "sideCount": 9}]|
|true|[{"name": "square", "sideCount": 4}, {"name": "rectangle", "sideCount": 4}, {"name": "육각형", "sideCount": 6}, {"name": "octogon", "sideCount": 8}, {"name": "decagon", "sideCount": 10}]|

## <a name="see-also"></a>참고 항목

[`make_list_if`](./makelistif-aggfunction.md) 연산자는 조건자를 `make_list` 수락 한다는 점을 제외 하 고와 비슷합니다.
