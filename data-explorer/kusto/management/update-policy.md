---
title: 업데이트 정책 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 업데이트 정책에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: 95952a6f4e7a8c0d1a5b4207742e15873eb44c91
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81519536"
---
# <a name="update-policy"></a>정책 업데이트

[업데이트 정책은](updatepolicy.md) 쿼리를 자동으로 실행하고 데이터를 다른 테이블로 수집할 때 결과를 수집하기 위한 테이블 수준 정책 개체입니다.

## <a name="show-update-policy"></a>업데이트 정책 표시

이 명령은 지정된 테이블의 업데이트 정책 또는 테이블 이름으로 `*` 사용되는 경우 기본 데이터베이스의 모든 테이블을 반환합니다.

**구문**

* `.show``table` *테이블 이름* `policy``update`
* `.show``table` *데이터베이스 이름*`.`*테이블 이름* `policy``update`
* `.show` `table` `*` `policy` `update`

**반환**

이 명령은 다음 열을 가진 테이블당 하나의 레코드가 있는 테이블을 반환합니다.

|열    |Type    |Description                                                                                                                                                           |
|----------|--------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|EntityName|`string`|업데이트 정책이 정의된 엔터티의 이름                                                                                                                |
|정책  |`string`|엔터티에 대해 정의된 모든 업데이트 정책을 나타내는 JSON 배열, [업데이트 정책 개체로](updatepolicy.md#the-update-policy-object) 서식이 지정|

**예제**

```kusto
.show table DerivedTableX policy update 
```

|EntityName        |정책                                                                                                                                    |
|------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
|[테스트DB]. [파생 테이블X]|[{"IsEnabled": true, "소스": "MyTableX","쿼리": "MyUpdateFunction()","IsTransactional": false,"전파 속성": false}]|

## <a name="alter-update-policy"></a>업데이트 정책 변경

이 명령은 지정된 테이블의 업데이트 정책을 설정합니다.

**구문**

* `.alter``table` *테이블 이름* `policy` `update` *배열OfUpdatePolicy개체*
* `.alter``table` *데이터베이스 이름*`.`*테이블 이름* `policy` `update` *배열OfUpdatePolicy개체*

*ArrayOfUpdatePolicyObjects는* 정의된 업데이트 정책 개체가 0개 이상인 JSON 배열입니다.

**참고 사항**

1. 업데이트 정책 개체의 속성에 대해 `Query` 저장된 함수를 사용하는 것이 좋습니다.
   이렇게 하면 전체 정책 개체 대신 함수 정의만 쉽게 수정할 수 있습니다.

2. 다음 유효성 검사는 업데이트 정책에서 설정 될 때 수행됩니다 `IsEnabled` (경우 `true`설정 된 경우) :
    1. `Source`: 테이블이 대상 데이터베이스에 있어야 합니다.
    2. `Query`: 
        * 스키마에 의해 정의된 스키마는 대상 테이블 중 하나와 일치해야 합니다. 
        * 쿼리는 업데이트 `source` 정책의 테이블을 참조해야 합니다. 소스를 *참조하지* 않는 업데이트 정책 쿼리를 정의하는 `AllowUnreferencedSourceTable=true` 것은 속성과 함께 설정하여 가능하지만(아래 예제 참조) 성능 상의 이유로 일반적으로 권장되지 않습니다(원본 테이블을 사용할 때마다 다른 테이블의 *모든* 레코드가 업데이트 정책 실행에 고려됨).
    3. 정책은 대상 데이터베이스의 업데이트 정책 체인에서 주기가 만들어지는 것은 아닙니다.
    4. 의 경우 권한은 `TableAdmin` (원본 테이블)에 대해서도 `Source` 확인됩니다. `IsTransactional` `true`
  
3. 소스 테이블에 대한 각 섭취에서 실행되도록 적용하기 전에 성능에 대한 업데이트 정책 [here](updatepolicy.md#testing-an-update-policys-performance-impact)/ 함수를 테스트하십시오.

**반환**

명령은 테이블의 업데이트 정책 개체(정의된 현재 정책(있는 경우 재정의)를 설정한 다음 해당 [.show table 업데이트 정책](#show-update-policy) 명령의 출력을 반환합니다.

**예제**

```kusto
// Creating function that will be used for update
.create function 
MyUpdateFunction()
{
    MyTableX
    | where ColumnA == 'some-string'
    | summarize MyCount=count() by ColumnB, Key=ColumnC
    | join (OtherTable | project OtherColumnZ, Key=OtherColumnC) on Key
    | project ColumnB, ColumnZ=OtherColumnZ, Key, MyCount
}

// Creating the target table (in case it doesn't already exist)
.set-or-append DerivedTableX <| MyUpdateFunction() | limit 0

// Use update policy on table DerivedTableX
.alter table DerivedTableX policy update
@'[{"IsEnabled": true, "Source": "MyTableX", "Query": "MyUpdateFunction()", "IsTransactional": false, "PropagateIngestionProperties": false}]'
```

- 이 경우 `MyTableX`원본 테이블에 대한 수집이 발생하면 해당 테이블에 1개 이상의 익스텐션(데이터 샤드)이 만들어집니다.
- 업데이트 `Query` 정책 개체에 정의된(이 경우)는 `MyUpdateFunction()`해당 익스텐트에서만 실행되며 전체 테이블에서 실행되지 않습니다.
  - 이 "범위 지정"은 내부적으로 자동으로 수행되며 `Query`을 정의할 때 처리해서는 안 됩니다.
  - 파생 된 테이블 (이 경우)에 `DerivedTableX`섭취 할 때 새로 가져온 레코드 (각 섭취 작업에서 다른)만 고려됩니다.


```kusto
// The following example will throw an error for not referencing the source table in the update policy query
// The policy's source table is MyTableX, whereas the query only references MyOtherTable. 
.alter table DerivedTableX policy update
@'[{"IsEnabled": true, "Source": "MyTableX", "Query": "MyOtherTable", "IsTransactional": false, "PropagateIngestionProperties": false}]'

// Adding the following properties will suppress the error (but is not recommended)
.alter table DerivedTableX policy update with (AllowUnreferencedSourceTable=true)
@'[{"IsEnabled": true, "Source": "MyTableX", "Query": "MyOtherTable", "IsTransactional": false, "PropagateIngestionProperties": false}]'

```

## <a name="alter-merge-table-table-policy-update"></a>.alter-merge 테이블 테이블 정책 업데이트

이 명령은 지정된 테이블의 업데이트 정책을 수정합니다.

**구문**

* `.alter-merge``table` *테이블 이름* `policy` `update` *배열OfUpdatePolicy개체*
* `.alter-merge``table` *데이터베이스 이름*`.`*테이블 이름* `policy` `update` *배열OfUpdatePolicy개체*

*ArrayOfUpdatePolicyObjects는* 정의된 업데이트 정책 개체가 0개 이상인 JSON 배열입니다.

**참고 사항**

1. 업데이트 정책 개체의 쿼리 속성을 대량 으로 구현하기 위해 저장된 함수를 사용하는 것이 좋습니다. 이렇게 하면 전체 정책 개체 대신 함수 정의만 쉽게 수정할 수 있습니다.

2. `alter` 명령에 대해 명령이 수행되는 경우 업데이트 정책에서 수행된 것과 동일한 유효성 검사가 수행됩니다. `alter-merge`

**반환**

명령은 테이블의 업데이트 정책 개체(정의된 현재 정책(있는 경우 재정의)에 추가된 다음 해당 [.show table table 업데이트 정책](#show-update-policy) 명령의 출력을 반환합니다.

**예제**

```kusto
.alter-merge table DerivedTableX policy update 
@'[{"IsEnabled": true, "Source": "MyTableY", "Query": "MyUpdateFunction()", "IsTransactional": false}]'  
``` 

## <a name="delete-table-table-policy-update"></a>.delete 테이블 테이블 정책 업데이트

지정된 테이블의 업데이트 정책을 삭제합니다.

**구문**

* `.delete``table` *테이블 이름* `policy``update`
* `.delete``table` *데이터베이스 이름*`.`*테이블 이름* `policy``update`

**반환**

이 명령은 테이블의 업데이트 정책 개체를 삭제한 다음 해당 [.show table table 업데이트 정책](#show-update-policy) 명령의 출력을 반환합니다.

**예제**

```kusto
.delete table DerivedTableX policy update 
```