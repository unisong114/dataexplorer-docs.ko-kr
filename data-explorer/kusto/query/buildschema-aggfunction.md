---
title: buildschema () (집계 함수)-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 buildschema () (집계 함수)에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 3fb6ae643bb4350cea1ffef4493625bc9c7d191d
ms.sourcegitcommit: 05489ce5257c0052aee214a31562578b0ff403e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88793575"
---
# <a name="buildschema-aggregation-function"></a>buildschema () (집계 함수)

*Dynamicexpr*의 모든 값을 인정 하는 최소 스키마를 반환 합니다.

* 집계의 컨텍스트에서만 [요약](summarizeoperator.md) 내에서 사용할 수 있습니다.

## <a name="syntax"></a>구문

`buildschema(` *dynamicexpr* 요약`)`

## <a name="arguments"></a>인수

* *Dynamicexpr*: 집계 계산에 사용 되는 식입니다. 매개 변수 열 형식은 이어야 합니다 `dynamic` . 

## <a name="returns"></a>반환

*`Expr`* 그룹 전체에서의 최대값입니다.

> [!TIP] 
> `buildschema(json_column)`에서 구문 오류가 발생 하는 경우:
>
> > *`json_column`동적 개체가 아닌 문자열 인가요?*
>
> 그런 다음 `buildschema(parsejson(json_column))` 를 사용 합니다.

## <a name="example"></a>예제

입력 열에 세 개의 동적 값이 있다고 가정 합니다.

||
|---|
|`{"x":1, "y":3.5}`|
|`{"x":"somevalue", "z":[1, 2, 3]}`|
|`{"y":{"w":"zzz"}, "t":["aa", "bb"], "z":["foo"]}`|
||

결과 스키마는 다음과 같습니다.

```kusto
{ 
    "x":["int", "string"],
    "y":["double", {"w": "string"}],
    "z":{"`indexer`": ["int", "string"]},
    "t":{"`indexer`": "string"}
}
```

스키마는 다음 정보를 알려줍니다.

* 루트 개체는 x, y, z 및 t 라는 네 개의 속성이 있는 컨테이너입니다.
* "Int" 형식 이거나 "string" 형식일 수 있는 "x" 속성입니다.
* "Double" 형식 이거나 "string" 형식의 "w" 라는 속성을 가진 다른 컨테이너에 해당 하는 "y" 속성입니다.
* ``indexer`` 키워드는 "z"와 "t"가 배열임을 나타냅니다.
* "Z" 배열의 각 항목은 "int" 형식 이거나 "string" 형식입니다.
* "t"는 문자열의 배열입니다.
* 모든 속성은 암시적으로 선택적이며 배열은 비어 있을 수 있습니다.

### <a name="schema-model"></a>스키마 모델

반환되는 스키마의 구문은 다음과 같습니다.

```output
Container ::= '{' Named-type* '}';
Named-type ::= (name | '"`indexer`"') ':' Type;
Type ::= Primitive-type | Union-type | Container;
Union-type ::= '[' Type* ']';
Primitive-type ::= "int" | "string" | ...;
```

값은 Kusto 동적 값으로 인코드된 TypeScript 형식 주석의 하위 집합에 해당 합니다. Typescript에서 예제 스키마는 다음과 같습니다.

```typescript
var someobject: 
{
    x?: (number | string),
    y?: (number | { w?: string}),
    z?: { [n:number] : (int | string)},
    t?: { [n:number]: string }
}
```
