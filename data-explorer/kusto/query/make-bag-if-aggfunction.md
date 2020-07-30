---
title: make_bag_if () (집계 함수)-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 make_bag_if () (집계 함수)에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 67e408653a4873dce3b5e8f21a91775573affbe2
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347020"
---
# <a name="make_bag_if-aggregation-function"></a>make_bag_if () (집계 함수)

`dynamic`그룹에서 *조건자* 가로 계산 되는 모든 *' Expr '* 값의 (JSON) 속성 모음 (사전)을 반환 `true` 합니다.

> [!NOTE]
> [요약](summarizeoperator.md)내의 집계 컨텍스트에서만 사용할 수 있습니다.

## <a name="syntax"></a>Syntax

`summarize``make_bag_if(` *Expr*, *Predicate* [ `,` *MaxSize*]`)`

## <a name="arguments"></a>인수

* *Expr*: `dynamic` 집계 계산에 사용 되는 형식의 식입니다.
* *조건자*: `true` 결과에 *' Expr '* 을 추가 하기 위해를 계산 해야 하는 조건자입니다.
* *MaxSize*: 반환 되는 최대 요소 수에 대 한 선택적 정수 제한입니다 (기본값은 *1048576*). MaxSize 값은 1048576를 초과할 수 없습니다.

## <a name="returns"></a>반환

`dynamic`그룹에서 *조건자* 가로 계산 되는 속성 모음 (사전) 인 *' Expr '* 의 모든 값에 대 한 (JSON) 속성 모음 (사전)을 반환 `true` 합니다.
사전 값이 아닌 값을 건너뜁니다.
키가 두 개 이상의 행에 표시 되는 경우이 키에 사용할 수 있는 값 중 임의의 값이 선택 됩니다.

> [!NOTE]
> [`make_bag`](./make-bag-aggfunction.md)함수는 조건자 식이 없는 make_bag_if ()와 유사 합니다.

## <a name="examples"></a>예제

```kusto
let T = datatable(prop:string, value:string, predicate:bool)
[
    "prop01", "val_a", true,
    "prop02", "val_b", false,
    "prop03", "val_c", true
];
T
| extend p = pack(prop, value)
| summarize dict=make_bag_if(p, predicate)

```

|dict|
|----|
|{"prop01": "val_a", "prop03": "val_c"} |

Make_bag_if () 출력의 자루 키를 열로 변환 하는 데 [bag_unpack ()](bag-unpackplugin.md) 플러그 인을 사용 합니다. 

```kusto
let T = datatable(prop:string, value:string, predicate:bool)
[
    "prop01", "val_a", true,
    "prop02", "val_b", false,
    "prop03", "val_c", true
];
T
| extend p = pack(prop, value)
| summarize bag=make_bag_if(p, predicate)
| evaluate bag_unpack(bag)

```

|prop01|prop03|
|---|---|
|val_a|val_c|
