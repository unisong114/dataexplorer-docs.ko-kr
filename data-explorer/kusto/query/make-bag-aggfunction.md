---
title: make_bag () (집계 함수)-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 make_bag () 집계 함수에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 936b8aedb4244836eff8c8618a53693395a0343c
ms.sourcegitcommit: 1bdbfdc04c4eac405f3931059bbeee2dedd87004
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/27/2020
ms.locfileid: "96303257"
---
# <a name="make_bag-aggregation-function"></a>make_bag () (집계 함수)

`dynamic`그룹에 있는 모든 값의 (JSON) 속성 모음 (사전)을 반환 합니다 *`Expr`* .

* [summarize](summarizeoperator.md) 내의 집계 컨텍스트에서만 사용할 수 있습니다.

## <a name="syntax"></a>Syntax

`summarize``make_bag(` *`Expr`* [ `,` *MaxSize*]`)`

## <a name="arguments"></a>인수

* *Expr*: `dynamic` 집계 계산에 사용 되는 형식의 식입니다.
* *MaxSize* 는 반환 되는 최대 요소 수에 대 한 선택적 정수 제한입니다. 기본값은 *1048576* 입니다. MaxSize 값은 *1048576* 를 초과할 수 없습니다.

> [!NOTE]
> `make_dictionary()` 는 레거시 및 사용 되지 않는 버전입니다 `make_bag()` . 레거시 버전의 기본 제한은 *MaxSize* = 128입니다.

## <a name="returns"></a>반환

`dynamic`그룹에 있는 모든 값의 (JSON) 속성 모음 (사전) (속성 모음)을 반환 *`Expr`* 합니다.
사전 값이 아닌 값을 건너뜁니다.
키가 두 개 이상의 행에 표시 되는 경우이 키에 사용할 수 있는 값 중 임의의 값이 선택 됩니다.

## <a name="see-also"></a>참고 항목

동적 JSON 개체를 속성 모음 키를 사용 하는 열로 확장 하는 데 [bag_unpack ()](bag-unpackplugin.md) 플러그 인을 사용 합니다. 

## <a name="examples"></a>예제

```kusto
let T = datatable(prop:string, value:string)
[
    "prop01", "val_a",
    "prop02", "val_b",
    "prop03", "val_c",
];
T
| extend p = pack(prop, value)
| summarize dict=make_bag(p)

```

|dict|
|----|
|{"prop01": "val_a", "prop02": "val_b", "prop03": "val_c"} |

Make_bag () 출력의 자루 키를 열로 변환 하는 데 [bag_unpack ()](bag-unpackplugin.md) 플러그 인을 사용 합니다. 

```kusto
let T = datatable(prop:string, value:string)
[
    "prop01", "val_a",
    "prop02", "val_b",
    "prop03", "val_c",
];
T
| extend p = pack(prop, value)
| summarize bag=make_bag(p)
| evaluate bag_unpack(bag) 

```

|prop01|prop02|prop03|
|---|---|---|
|val_a|val_b|val_c|
