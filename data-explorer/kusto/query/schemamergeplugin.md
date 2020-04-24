---
title: schema_merge 플러그인 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 schema_merge 플러그인에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/16/2020
ms.openlocfilehash: 67326a0e7a92d064613ee3a3de2851addb502fc9
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81509251"
---
# <a name="schema_merge-plugin"></a>schema_merge 플러그인

테이블 형식 스키마 정의를 통합 스키마로 병합합니다. 

스키마 정의는 [getschema](./getschemaoperator.md) 연산자에서 생성한 형식이 될 것으로 예상됩니다.

스키마 병합 작업 은 입력 스키마에 나타나고 데이터 형식을 공통 데이터 유형으로 줄이려고 시도하는 작업 조합을 병합합니다(데이터 형식을 줄일 수 없는 경우 문제가 있는 열에 오류가 표시됩니다).

```kusto
let Schema1=Table1 | getschema;
let Schema2=Table2 | getschema;
union Schema1, Schema2 | evaluate schema_merge()
```

**구문**

`T``|` *PreserveOrder* 보존 `evaluate` `schema_merge(``)`

**인수**

* *보존 순서*: (선택 `true`사항) 로 설정하면 첫 번째 테이블 스키마에 의해 정의된 열 순서가 유지되는 열 순서를 확인하기 위해 플러그인으로 이동합니다. 즉, 동일한 열이 여러 스키마에 나타나는 경우 열 서수는 첫 번째 스키마와 같아야 합니다. 기본값은 `true`여야 합니다.

**반환**

플러그인은 `schema_merge` 출력 simiar를 [가져옵니다 chema](./getschemaoperator.md) 연산자 반환합니다.

**예**

새 열이 추가된 스키마와 병합합니다.

```kusto
let schema1 = datatable(Uri:string, HttpStatus:int)[] | getschema;
let schema2 = datatable(Uri:string, HttpStatus:int, Referrer:string)[] | getschema;
union schema1, schema2 | evaluate schema_merge()
```

*결과*

|ColumnName | ColumnOrdinal | DataType | ColumnType|
|---|---|---|---|
|Uri|0|System.String|문자열|
|HttpStatus|1|System.Int32|int|
|Referrer|2|System.String|문자열|

다른 열 순서가 있는 스키마와`HttpStatus` 병합(새 `2` 변형에서 `1` 서수 변경됨).

```kusto
let schema1 = datatable(Uri:string, HttpStatus:int)[] | getschema;
let schema2 = datatable(Uri:string, Referrer:string, HttpStatus:int)[] | getschema;
union schema1, schema2 | evaluate schema_merge()
```

*결과*

|ColumnName | ColumnOrdinal | DataType | ColumnType|
|---|---|---|---|
|Uri|0|System.String|문자열|
|Referrer|1|System.String|문자열|
|HttpStatus|-1|오류(알 수 없는 CSL 유형:ERROR(열의 순서가 잘못))|오류(열의 순서가 잘못되었습니다)|

열 순서가 다르지만 `PreserveOrder` `false` 이번에는 설정된 스키마와 병합합니다.

```kusto
let schema1 = datatable(Uri:string, HttpStatus:int)[] | getschema;
let schema2 = datatable(Uri:string, Referrer:string, HttpStatus:int)[] | getschema;
union schema1, schema2 | evaluate schema_merge(PreserveOrder = false)
```

*결과*

|ColumnName | ColumnOrdinal | DataType | ColumnType|
|---|---|---|---|
|Uri|0|System.String|문자열
|Referrer|1|System.String|문자열
|HttpStatus|2|System.Int32|int|