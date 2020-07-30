---
title: bag_unpack 플러그 인-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 bag_unpack 플러그 인을 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 06/15/2020
ms.openlocfilehash: 6c91275320a5ec404b6cd5fcbe8c84b4123bd2de
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349349"
---
# <a name="bag_unpack-plugin"></a>bag_unpack 플러그 인

`bag_unpack`플러그 인은 `dynamic` 각 속성 모음 최상위 슬롯을 열로 처리 하 여 형식의 단일 열을 압축 합니다.

    T | evaluate bag_unpack(col1)

## <a name="syntax"></a>Syntax

*T* `|` `evaluate` `bag_unpack(` *열* [ `,` *outputcolumnprefix* ] [열 `,` *충돌* ] [ `,` *ignoredproperties* ]`)`

## <a name="arguments"></a>인수

* *T*: 열 *열* 을 압축을 풀 테이블 형식 입력입니다.
* *Column*: 압축을 풀 *T* 의 열입니다. 반드시 `dynamic` 형식이어야 합니다.
* *Outputcolumnprefix*: 플러그 인에서 생성 하는 모든 열에 추가할 일반적인 접두사입니다. 이 인수는 선택 사항입니다.
* 열 *충돌*: 열 충돌 해결을 위한 방향입니다. 이 인수는 선택 사항입니다. 인수가 제공 되는 경우 다음 값 중 하 나와 일치 하는 문자열 리터럴이 될 것으로 예상 됩니다.
    - `error`-Query에서 오류를 생성 합니다 (기본값).
    - `replace_source`-원본 열이 대체 됨
    - `keep_source`-원본 열이 유지 됩니다.
* *Ignoredproperties*: 무시할 모음 속성의 선택적 집합입니다. 인수를 제공 하는 경우 `dynamic` 하나 이상의 문자열 리터럴이 있는 배열의 상수가 되어야 합니다.

## <a name="returns"></a>반환

`bag_unpack`플러그 인은 레코드를 테이블 형식 입력 (*T*)으로 포함 하는 테이블을 반환 합니다. 테이블의 스키마는 다음과 같이 수정 된 테이블 형식 입력의 스키마와 동일 합니다.

* 지정한 입력 열 (*열*)이 제거 됩니다.
* 이 스키마는 *T*의 최상위 속성 모음 값에서 고유한 슬롯 수 만큼의 열로 확장 됩니다. 각 열의 이름은 각 슬롯의 이름에 해당 하며 필요에 따라 *Outputcolumnprefix*접두사가 붙습니다. 해당 형식은 슬롯의 형식으로, 동일한 슬롯의 모든 값이 동일한 형식이 면이 고, `dynamic` 값이 형식이 다른 경우입니다.

**참고**

플러그 인의 출력 스키마는 데이터 값에 따라 달라 지 며 데이터 자체로 "예측할 수 없음"으로 설정 됩니다. 다른 데이터 입력을 사용 하 여 플러그 인을 여러 번 실행 하면 다른 출력 스키마가 생성 될 수 있습니다.

플러그 인에 대 한 입력 데이터는 출력 스키마가 테이블 형식 스키마에 대 한 모든 규칙을 준수 해야 합니다. 특히 다음 사항에 주의하십시오.

* 출력 열 이름은 같은 이름으로 두 개의 열을 생성 하므로 압축을 풀 열 (*열*)이 아닌 경우에는 테이블 형식 입력 *t*의 기존 열과 같을 수 없습니다.

* 모든 슬롯 이름에 대해 *Outputcolumnprefix*접두사가 유효한 엔터티 이름 이어야 하 고 [식별자 명명 규칙](./schema-entities/entity-names.md#identifier-naming-rules)을 따라야 합니다.

## <a name="examples"></a>예제

### <a name="expand-a-bag"></a>모음 확장


<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(d:dynamic)
[
    dynamic({"Name": "John", "Age":20}),
    dynamic({"Name": "Dave", "Age":40}),
    dynamic({"Name": "Jasmine", "Age":30}),
]
| evaluate bag_unpack(d)
```

|Name  |연령|
|------|---|
|John  |20 |
|Dave  |40 |
|Jasmine|30 |


### <a name="expand-a-bag-with-outputcolumnprefix"></a>OutputColumnPrefix를 사용 하 여 모음 확장

모음을 확장 하 고 옵션을 사용 `OutputColumnPrefix` 하 여 접두사 ' Property_ '로 시작 하는 열 이름을 생성 합니다.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(d:dynamic)
[
    dynamic({"Name": "John", "Age":20}),
    dynamic({"Name": "Dave", "Age":40}),
    dynamic({"Name": "Jasmine", "Age":30}),
]
| evaluate bag_unpack(d, 'Property_')
```

|Property_Name|Property_Age|
|---|---|
|John|20|
|Dave|40|
|Jasmine|30|

### <a name="expand-a-bag-with-columnsconflict"></a>펼친 충돌으로 모음 확장

모음을 확장 하 고 옵션을 사용 `columnsConflict` 하 여 연산자가 생성 한 열과 기존 열 간의 충돌을 해결 `bag_unpack()` 합니다.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(Name:string, d:dynamic)
[
    'Old_name', dynamic({"Name": "John", "Age":20}),
    'Old_name', dynamic({"Name": "Dave", "Age":40}),
    'Old_name', dynamic({"Name": "Jasmine", "Age":30}),
]
| evaluate bag_unpack(d, columnsConflict='replace_source') // Use new name
```

|Name|연령|
|---|---|
|John|20|
|Dave|40|
|Jasmine|30|

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(Name:string, d:dynamic)
[
    'Old_name', dynamic({"Name": "John", "Age":20}),
    'Old_name', dynamic({"Name": "Dave", "Age":40}),
    'Old_name', dynamic({"Name": "Jasmine", "Age":30}),
]
| evaluate bag_unpack(d, columnsConflict='keep_source') // Keep old name
```

|Name|연령|
|---|---|
|Old_name|20|
|Old_name|40|
|Old_name|30|

### <a name="expand-a-bag-with-ignoredproperties"></a>IgnoredProperties를 사용 하 여 모음 확장

모음을 확장 하 고 옵션을 사용 `ignoredProperties` 하 여 속성 모음의 특정 속성을 무시 합니다.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(d:dynamic)
[
    dynamic({"Name": "John", "Age":20, "Address": "Address-1" }),
    dynamic({"Name": "Dave", "Age":40, "Address": "Address-2"}),
    dynamic({"Name": "Jasmine", "Age":30, "Address": "Address-3"}),
]
// Ignore 'Age' and 'Address' properties
| evaluate bag_unpack(d, ignoredProperties=dynamic(['Address', 'Age']))
```

|Name|
|---|
|John|
|Dave|
|Jasmine|
