---
title: make_bag_if() (집계 함수) - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 make_bag_if(집계 함수)에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 201de637df387bb8925995a5e52c048255d1535c
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81512974"
---
# <a name="make_bag_if-aggregation-function"></a>make_bag_if() (집계 함수)

`dynamic` *조건어가* `true`평가하는 그룹의 *Expr* 의 모든 값에 대한 JSON(사전) 속성 백(사전)을 반환합니다.

* 요약 내부 집계의 컨텍스트에서만 사용할 수 [있습니다.](summarizeoperator.md)

**구문**

`summarize``make_bag_if(` *Expr*, *조건자* [`,` *MaxSize*]`)`

**인수**

* *Expr*: 집계 `dynamic` 계산에 사용되는 형식의 표현식입니다.
* *조건어*: `true` *Expr이* 결과에 추가되기 위해 을 평가해야 하는 조건어입니다.
* *MaxSize는* 반환되는 최대 요소 수에 대한 선택적 정수 제한입니다(기본값은 *1048576).* MaxSize 값은 1048576을 초과할 수 없습니다.

**반환**

`dynamic` *조건어가* 평가하는 속성 백(사전)인 그룹의 `true` *Expr* 의 모든 값에 대한 (JSON) 속성 백(사전)을 반환합니다.
사전이 아닌 값은 건너뜁니다.
키가 두 개 이상의 행에 나타나면 임의의 값(이 키에 대해 가능한 값 중)이 선택됩니다.

**참고 항목**

[`make_bag`](./make-bag-aggfunction.md)조건자 식 없이 동일한 작업을 수행합니다.

**예**

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
|{ "prop01": "val_a", "prop03": "val_c" } |

make_bag_if() 출력의 가방 키를 열로 변환하기 위해 [bag_unpack()](bag-unpackplugin.md) 플러그인을 사용합니다. 

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