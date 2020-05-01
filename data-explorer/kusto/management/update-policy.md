---
title: Kusto 업데이트 정책 관리-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 업데이트 정책에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: 320824b4f614ea809141167c1284282b1ef641cf
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/01/2020
ms.locfileid: "82616561"
---
# <a name="update-policy"></a>정책 업데이트

[업데이트 정책은](updatepolicy.md) 쿼리를 자동으로 실행 하 고 데이터를 다른 테이블로 수집 때 해당 결과를 수집 하는 테이블 수준 정책 개체입니다.

## <a name="show-update-policy"></a>업데이트 정책 표시

이 명령은 지정 된 테이블의 업데이트 정책 또는를 테이블 이름으로 사용 하는 경우 `*` 기본 데이터베이스의 모든 테이블을 반환 합니다.

**구문**

* `.show``table` *TableName* TableName `policy``update`
* `.show``table` *DatabaseName*DatabaseName`.`*TableName* TableName `policy``update`
* `.show` `table` `*` `policy` `update`

**반환**

이 명령은 테이블당 하나의 레코드가 있는 테이블을 반환 합니다 .이 테이블에는 다음 열이 있습니다.

|열    |Type    |설명                                                                                                                                                           |
|----------|--------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|EntityName|`string`|업데이트 정책이 정의 된 엔터티의 이름입니다.                                                                                                                |
|정책  |`string`|엔터티에 대해 정의 된 모든 업데이트 정책을 나타내는 JSON 배열로, [업데이트 정책 개체로](updatepolicy.md#the-update-policy-object) 형식이 지정 됩니다.|

**예제**

```kusto
.show table DerivedTableX policy update 
```

|EntityName        |정책                                                                                                                                    |
|------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
|[TestDB]. [DerivedTableX]|[{"IsEnabled": true, "Source": "MyTableX", "Query": "MyUpdateFunction ()", "IsTransactional": false, "PropagateIngestionProperties": false}]|

## <a name="alter-update-policy"></a>업데이트 정책 변경

이 명령은 지정 된 테이블의 업데이트 정책을 설정 합니다.

**구문**

* `.alter``table` *TableName* TableName `policy` *ArrayOfUpdatePolicyObjects* ArrayOfUpdatePolicyObjects `update`
* `.alter``table` *DatabaseName* `update` *ArrayOfUpdatePolicyObjects* *TableName* TableName ArrayOfUpdatePolicyObjects`.` `policy`

*ArrayOfUpdatePolicyObjects* 는 0 개 이상의 업데이트 정책 개체가 정의 된 JSON 배열입니다.

**참고 사항**

1. 업데이트 정책 개체의 `Query` 속성에 대해 저장 된 함수를 사용 하는 것이 좋습니다.
   이렇게 하면 전체 정책 개체 대신 함수 정의만 쉽게 수정할 수 있습니다.

2. 를 설정 하는 경우 업데이트 정책에 대해 다음 유효성 검사가 수행 됩니다 ( `IsEnabled` 가로 `true`설정 된 경우).
    1. `Source`: 테이블이 대상 데이터베이스에 있어야 합니다.
    2. `Query`: 
        * 스키마에서 정의한 스키마는 대상 테이블의 스키마와 일치 해야 합니다. 
        * 쿼리는 업데이트 정책 테이블 `source` 을 참조 해야 합니다. 소스를 참조 *하지* 않는 업데이트 정책 쿼리를 정의 하는 것이 가능 `AllowUnreferencedSourceTable=true` 속성 (아래 예제 참조)에서를 설정 하 여 가능 하지만 일반적으로 성능상의 이유로 사용 하지 않는 것이 좋습니다. 즉, 원본 테이블에 대 한 모든 수집에서 다른 테이블의 *모든* 레코드가 업데이트 정책 실행에 대해 고려 됨을 의미 합니다.
    3. 정책은 대상 데이터베이스의 업데이트 정책 체인에 생성 되는 주기가 발생 하지 않습니다.
    4. 가로 `IsTransactional` `true`설정 된 경우에 `TableAdmin` 는 (원본 테이블 `Source` )에 대해서도 사용 권한이 확인 됩니다.
  
3. 원본 테이블에 대 한 각 수집에서 실행 되도록 업데이트 정책/함수를 테스트 해야 합니다. 자세한 내용은 [여기](updatepolicy.md#testing-an-update-policys-performance-impact)를 참조 하세요.

**반환**

이 명령은 테이블의 업데이트 정책 개체 (정의 된 모든 현재 정책 재정의)를 설정 하 고 해당 하는 [. 테이블 테이블 업데이트 정책 표시](#show-update-policy) 명령의 출력을 반환 합니다.

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

- 원본 테이블 (이 경우 `MyTableX`)에 대 한 수집이 발생 하면 해당 테이블에 하나 이상의 익스텐트 (데이터 분할)가 생성 됩니다.
- `Query` 업데이트 정책 개체에 정의 된 (이 경우 `MyUpdateFunction()`)는 해당 범위 에서만 실행 되며 전체 테이블에서 실행 되지 않습니다.
  - 이 "범위 지정"은 내부적으로 자동으로 수행 되며를 `Query`정의할 때 처리 되지 않습니다.
  - 파생 테이블 (이 경우 `DerivedTableX`)에 수집 때 새로 수집 레코드 (각 수집 작업 마다 다름)만 고려 됩니다.


```kusto
// The following example will throw an error for not referencing the source table in the update policy query
// The policy's source table is MyTableX, whereas the query only references MyOtherTable. 
.alter table DerivedTableX policy update
@'[{"IsEnabled": true, "Source": "MyTableX", "Query": "MyOtherTable", "IsTransactional": false, "PropagateIngestionProperties": false}]'

// Adding the following properties will suppress the error (but is not recommended)
.alter table DerivedTableX policy update with (AllowUnreferencedSourceTable=true)
@'[{"IsEnabled": true, "Source": "MyTableX", "Query": "MyOtherTable", "IsTransactional": false, "PropagateIngestionProperties": false}]'

```

## <a name="alter-merge-table-table-policy-update"></a>. alter-병합 테이블 테이블 정책 업데이트

이 명령은 지정 된 테이블의 업데이트 정책을 수정 합니다.

**구문**

* `.alter-merge``table` *TableName* TableName `policy` *ArrayOfUpdatePolicyObjects* ArrayOfUpdatePolicyObjects `update`
* `.alter-merge``table` *DatabaseName* `update` *ArrayOfUpdatePolicyObjects* *TableName* TableName ArrayOfUpdatePolicyObjects`.` `policy`

*ArrayOfUpdatePolicyObjects* 는 0 개 이상의 업데이트 정책 개체가 정의 된 JSON 배열입니다.

**참고 사항**

1. 업데이트 정책 개체의 쿼리 속성을 대량으로 구현 하는 데 저장 된 함수를 사용 하는 것이 좋습니다. 이렇게 하면 전체 정책 개체 대신 함수 정의만 쉽게 수정할 수 있습니다.

2. 명령을 실행 하는 경우 `alter` 업데이트 정책에서 수행 되는 동일한 유효성 검사가 `alter-merge` 명령에 대해 수행 됩니다.

**반환**

이 명령은 테이블의 업데이트 정책 개체에 추가 하 고 (있는 경우 정의 된 현재 정책 재정의) 해당 [. 테이블 테이블 업데이트 정책 표시](#show-update-policy) 명령의 출력을 반환 합니다.

**예제**

```kusto
.alter-merge table DerivedTableX policy update 
@'[{"IsEnabled": true, "Source": "MyTableY", "Query": "MyUpdateFunction()", "IsTransactional": false}]'  
``` 

## <a name="delete-table-table-policy-update"></a>. 테이블 테이블 정책 업데이트 삭제

지정 된 테이블의 업데이트 정책을 삭제 합니다.

**구문**

* `.delete``table` *TableName* TableName `policy``update`
* `.delete``table` *DatabaseName*DatabaseName`.`*TableName* TableName `policy``update`

**반환**

이 명령은 테이블의 업데이트 정책 개체를 삭제 한 다음 해당 [. 테이블 테이블 업데이트 정책 표시](#show-update-policy) 명령의 출력을 반환 합니다.

**예제**

```kusto
.delete table DerivedTableX policy update 
```