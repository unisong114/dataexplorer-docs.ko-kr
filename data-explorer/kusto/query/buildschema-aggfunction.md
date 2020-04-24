---
title: 빌드스키마() (집계 함수) - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 빌드스키마(집계 함수)에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: d0faceae970034ae96ced2b8958af4f16cb5dff4
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81517292"
---
# <a name="buildschema-aggregation-function"></a>빌드스키마() (집계 함수)

*DynamicExpr의*모든 값을 인정하는 최소 스키마를 반환합니다.

* 요약 내부 집계의 컨텍스트에서만 사용할 수 [있습니다.](summarizeoperator.md)

**구문**

동적 `buildschema(` *Expr* 요약`)`

**인수**

* *DynamicExpr*: 집계 계산에 사용되는 표현식입니다. 매개 변수 열 `dynamic`유형은 이어야 합니다. 

**반환**

그룹 전체의 *Expr* 최대값입니다.

> [!TIP] 
> 구문 오류가 있는 경우: `buildschema(json_column)` * `json_column` 문자열이 동적 개체가 아닌 문자열입니까?* 그렇다면 을 사용해야 `buildschema(parsejson(json_column))`합니다.

**예제**

입력된 열이 동적 값 세 개를 가진다고 가정합니다.

||
|---|
|`{"x":1, "y":3.5}`|
|`{"x":"somevalue", "z":[1, 2, 3]}`|
|`{"y":{"w":"zzz"}, "t":["aa", "bb"], "z":["foo"]}`|
||

결과 스키마는 다음과 같습니다.

    { 
      "x":["int", "string"], 
      "y":["double", {"w": "string"}], 
      "z":{"`indexer`": ["int", "string"]}, 
      "t":{"`indexer`": "string"} 
    }

스키마는 다음 정보를 알려줍니다.

* 루트 개체는 x, y, z 및 t라는 네 속성을 가진 컨테이너입니다.
* 형식 “Int” 또는 형식 “string” "x"라는 속성.
* 형식이 “double”인 “y”라는 속성 또는 형식 “string”의 “w”라는 속성을 가진 다른 컨테이너.
* ``indexer`` 키워드는 "z"와 "t"가 배열임을 나타냅니다.
* "z" 배열의 각 항목은 int 또는 문자열입니다.
* "t"는 문자열의 배열입니다.
* 모든 속성은 암시적으로 선택적이며 배열은 비어 있을 수 있습니다.

### <a name="schema-model"></a>스키마 모델

반환되는 스키마의 구문은 다음과 같습니다.

    Container ::= '{' Named-type* '}';
    Named-type ::= (name | '"`indexer`"') ':' Type;
    Type ::= Primitive-type | Union-type | Container;
    Union-type ::= '[' Type* ']';
    Primitive-type ::= "int" | "string" | ...;

Kusto 동적 값으로 인코딩된 TypeScript 형식 주석의 하위 집합과 동일합니다. Typescript에서 예제 스키마는 다음과 같습니다.

    var someobject: 
    { 
      x?: (number | string), 
      y?: (number | { w?: string}), 
      z?: { [n:number] : (int | string)},
      t?: { [n:number]: string } 
    }