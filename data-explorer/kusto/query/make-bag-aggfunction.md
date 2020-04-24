---
title: make_bag() (집계 기능) - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 make_bag(집계 함수)에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 16f5f5663c4807a766d99c12020ff0a46c4db336
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81512991"
---
# <a name="make_bag-aggregation-function"></a>make_bag() (집계 함수)

그룹의 `dynamic` *Expr* 의 모든 값에 대한 JSON(사전) 속성 백(사전)을 반환합니다.

* 요약 내부 집계의 컨텍스트에서만 사용할 수 [있습니다.](summarizeoperator.md)

**구문**

`summarize``make_bag(` *Expr* `,` [ *최대 크기*]`)`

**인수**

* *Expr*: 집계 `dynamic` 계산에 사용되는 형식의 표현식입니다.
* *MaxSize는* 반환되는 최대 요소 수에 대한 선택적 정수 제한입니다(기본값은 *1048576).* MaxSize 값은 1048576을 초과할 수 없습니다.

**참고**

이 함수의 레거시 및 사용되지 `make_dictionary()` 않는 변형: *MaxSize* = 128의 기본 제한이 있습니다.

**반환**

속성 `dynamic` 가방(사전)인 그룹의 *Expr* 의 모든 값에 대한 (JSON) 속성 가방(사전)을 반환합니다.
사전이 아닌 값은 건너뜁니다.
키가 두 개 이상의 행에 나타나면 임의의 값(이 키에 대해 가능한 값 중)이 선택됩니다.

**참고 항목**

속성 가방 키를 사용하여 동적 JSON 개체를 열로 확장하기 위해 [bag_unpack()](bag-unpackplugin.md) 플러그인을 사용합니다. 

**예**

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
|{ "prop01": "val_a", "prop02": "val_b", "prop03": "val_c" } |

make_bag() 출력의 가방 키를 열로 변환하기 위해 [bag_unpack()](bag-unpackplugin.md) 플러그인을 사용합니다. 

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