---
title: Kusto 업데이트 정책 관리-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 업데이트 정책에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/04/2020
ms.openlocfilehash: 111110ac69e726c8367af4a2741a79061df7531a
ms.sourcegitcommit: 3dfaaa5567f8a5598702d52e4aa787d4249824d4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/05/2020
ms.locfileid: "87803865"
---
# <a name="update-policy-commands"></a>정책 업데이트 명령

[업데이트 정책은](updatepolicy.md) 쿼리를 자동으로 실행 한 다음 데이터를 다른 테이블에 수집 때 결과를 수집 하는 테이블 수준 정책 개체입니다.

## <a name="show-update-policy"></a>업데이트 정책 표시

이 명령은 지정 된 테이블의 업데이트 정책 또는 `*` 를 테이블 이름으로 사용 하는 경우 기본 데이터베이스의 모든 테이블을 반환 합니다.

### <a name="syntax"></a>구문

* `.show``table` *TableName* TableName `policy``update`
* `.show``table` *DatabaseName* `.` *TableName* TableName `policy``update`
* `.show` `table` `*` `policy` `update`

### <a name="returns"></a>반환

이 명령은 테이블당 하나의 레코드가 있는 테이블을 반환 합니다.

|Column    |형식    |Description                                                                                                                                                           |
|----------|--------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|EntityName|`string`|업데이트 정책이 정의 된 엔터티의 이름입니다.                                                                                                                |
|정책  |`string`|엔터티에 대해 정의 된 모든 업데이트 정책을 나타내는 JSON 배열로, [업데이트 정책 개체로](updatepolicy.md#the-update-policy-object) 형식이 지정 됩니다.|

### <a name="example"></a>예제

```kusto
.show table DerivedTableX policy update 
```

|EntityName        |정책                                                                                                                                    |
|------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
|[TestDB]. [DerivedTableX]|[{"IsEnabled": true, "Source": "MyTableX", "Query": "MyUpdateFunction ()", "IsTransactional": false, "PropagateIngestionProperties": false}]|

## <a name="alter-update-policy"></a>업데이트 정책 변경

이 명령은 지정 된 테이블의 업데이트 정책을 설정 합니다.

### <a name="syntax"></a>구문

* `.alter``table` *TableName* `policy` TableName `update` *ArrayOfUpdatePolicyObjects*
* `.alter``table` *DatabaseName* `.` *TableName* `policy` `update` *ArrayOfUpdatePolicyObjects*

*ArrayOfUpdatePolicyObjects* 는 0 개 이상의 업데이트 정책 개체가 정의 된 JSON 배열입니다.

> [!NOTE]
> * 업데이트 정책 개체의 속성에 대해 저장 된 함수를 사용 `Query` 합니다.
   전체 정책 개체 대신 함수 정의를 수정 해야 합니다.
> * `IsEnabled`가로 설정 된 경우 `true` 설정 되는 업데이트 정책에 대해 다음 유효성 검사가 수행 됩니다.
>    * `Source`-테이블이 대상 데이터베이스에 있는지 검사 합니다.
>    * `Query` 
>        * 스키마에 정의 된 스키마가 대상 테이블의 스키마와 일치 하는지 확인 합니다.
>        * 쿼리가 업데이트 정책 테이블을 참조 하는지 확인 `source` 합니다. 
        소스를 참조 하지 않는 업데이트 정책 쿼리를 정의 하는 방법은 `AllowUnreferencedSourceTable=true` *with* properties (아래 예제 참조)에서를 설정 하 여 수행할 수 있지만 성능 문제 때문에 권장 되지 않습니다. 원본 테이블에 대 한 모든 수집에 대해 다른 테이블의 모든 레코드를 업데이트 정책 실행에 대해 고려 합니다.
 >       * 정책으로 인해 대상 데이터베이스의 업데이트 정책 체인에서 순환이 생성 되지 않는지 확인 합니다.
 > * `IsTransactional`가로 설정 된 경우는 `true` `TableAdmin` `Source` (원본 테이블)에 대해서도 사용 권한이 확인 되는지 확인 합니다.
 > * 원본 테이블에 대 한 각 수집에서 실행 되도록 업데이트 정책 또는 성능 기능을 테스트 합니다. 자세한 내용은 [업데이트 정책 성능 영향 테스트](updatepolicy.md#performance-impact)를 참조 하십시오.

### <a name="returns"></a>반환

이 명령은 테이블의 업데이트 정책 개체를 설정 하 고 현재 정책을 재정의 한 다음 해당 [. 테이블 업데이트 정책 표시](#show-update-policy) 명령의 출력을 반환 합니다.

### <a name="example"></a>예제

```kusto
// Create a function that will be used for update
.create function 
MyUpdateFunction()
{
    MyTableX
    | where ColumnA == 'some-string'
    | summarize MyCount=count() by ColumnB, Key=ColumnC
    | join (OtherTable | project OtherColumnZ, Key=OtherColumnC) on Key
    | project ColumnB, ColumnZ=OtherColumnZ, Key, MyCount
}

// Create the target table (if it doesn't already exist)
.set-or-append DerivedTableX <| MyUpdateFunction() | limit 0

// Use update policy on table DerivedTableX
.alter table DerivedTableX policy update
@'[{"IsEnabled": true, "Source": "MyTableX", "Query": "MyUpdateFunction()", "IsTransactional": false, "PropagateIngestionProperties": false}]'
```

* 원본 테이블에 대 한 수집이 발생 하면이 경우 `MyTableX` 하나 이상의 익스텐트 (데이터 분할)가 해당 테이블에 만들어집니다.
* `Query`업데이트 정책 개체에 정의 된 (이 경우)는 `MyUpdateFunction()` 해당 범위 에서만 실행 되며 전체 테이블에서 실행 되지 않습니다.
  * "범위 지정"은 내부적으로 자동으로 수행 되며를 정의할 때 처리 되지 않습니다 `Query` .
  * 각 수집 작업에서 다른 새 수집 레코드만 파생 테이블에 수집 때 고려 됩니다 `DerivedTableX` .

```kusto
// The following example will throw an error for not referencing the source table in the update policy query
// The policy's source table is MyTableX, whereas the query only references MyOtherTable. 
.alter table DerivedTableX policy update
@'[{"IsEnabled": true, "Source": "MyTableX", "Query": "MyOtherTable", "IsTransactional": false, "PropagateIngestionProperties": false}]'

// Adding the following properties will suppress the error (but is not recommended)
.alter table DerivedTableX policy update with (AllowUnreferencedSourceTable=true)
@'[{"IsEnabled": true, "Source": "MyTableX", "Query": "MyOtherTable", "IsTransactional": false, "PropagateIngestionProperties": false}]'

```

## <a name="alter-merge-table-tablename-policy-update"></a>. alter-병합 테이블 *TableName* 정책 업데이트

이 명령은 지정 된 테이블의 업데이트 정책을 수정 합니다.

**구문**

* `.alter-merge``table` *TableName* `policy` TableName `update` *ArrayOfUpdatePolicyObjects*
* `.alter-merge``table` *DatabaseName* `.` *TableName* `policy` `update` *ArrayOfUpdatePolicyObjects*

*ArrayOfUpdatePolicyObjects* 는 0 개 이상의 업데이트 정책 개체가 정의 된 JSON 배열입니다.

> [!NOTE]
> * 업데이트 정책 개체의 쿼리 속성을 대량으로 구현 하기 위해 저장 된 함수를 사용 합니다. 
     전체 정책 개체 대신 함수 정의를 수정 해야 합니다.
> * 유효성 검사는 명령에서 수행 되는 유효성 검사와 동일 합니다 `alter` .

**반환**

이 명령은 테이블의 업데이트 정책 개체에 추가 하 고 현재 정책을 무시 한 다음 해당 [. show Table *TableName* update policy](#show-update-policy) 명령의 출력을 반환 합니다.

**예제**

```kusto
.alter-merge table DerivedTableX policy update 
@'[{"IsEnabled": true, "Source": "MyTableY", "Query": "MyUpdateFunction()", "IsTransactional": false}]'  
``` 

## <a name="delete-table-tablename-policy-update"></a>. 테이블 *TableName* 정책 업데이트 삭제

지정 된 테이블의 업데이트 정책을 삭제 합니다.

**구문**

* `.delete``table` *TableName* TableName `policy``update`
* `.delete``table` *DatabaseName* `.` *TableName* TableName `policy``update`

**반환**

이 명령은 테이블의 업데이트 정책 개체를 삭제 한 다음 해당 [. show Table *TableName* update policy](#show-update-policy) 명령의 출력을 반환 합니다.

**예제**

```kusto
.delete table DerivedTableX policy update 
```
