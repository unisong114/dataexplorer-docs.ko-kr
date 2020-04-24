---
title: make_list_if() (집계 기능) - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 make_list_if(집계 함수)에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: b34c1dad7be709145c622c97b357734c25292bba
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81512736"
---
# <a name="make_list_if-aggregation-function"></a>make_list_if() (집계 함수)

*조건어가* `dynamic` `true`평가하는 그룹의 *Expr* 의 모든 값에 대한 JSON(JSON) 배열을 반환합니다.

* 요약 내부 집계의 컨텍스트에서만 사용할 수 [있습니다.](summarizeoperator.md)

**구문**

`summarize``make_list_if(` *Expr*, *조건자* [`,` *MaxSize*]`)`

**인수**

* *Expr*: 집계 계산에 사용되는 표현식입니다.
* *조건어*: `true` *Expr이* 결과에 추가되기 위해 을 평가해야 하는 조건어입니다.
* *MaxSize는* 반환되는 최대 요소 수에 대한 선택적 정수 제한입니다(기본값은 *1048576).* MaxSize 값은 1048576을 초과할 수 없습니다.

**반환**

*조건어가* `dynamic` `true`평가하는 그룹의 *Expr* 의 모든 값에 대한 JSON(JSON) 배열을 반환합니다.
연산자의 `summarize` 입력이 정렬되지 않으면 결과 배열의 요소 순서가 정의되지 않습니다.
`summarize` 연산자의 입력이 정렬되면 결과 배열의 요소 순서가 입력의 입력을 추적합니다.

**예제**

```kusto
let T = datatable(name:string, day_of_birth:long)
[
   "John", 9,
   "Paul", 18,
   "George", 25,
   "Ringo", 7
];
T
| summarize make_list_if(name, strlen(name) > 4)
```

|list_name|
|----|
|["조지", "링고"]|

**참고 항목**

[`make_list`](./makelist-aggfunction.md)조건자 식 없이 동일한 작업을 수행합니다.