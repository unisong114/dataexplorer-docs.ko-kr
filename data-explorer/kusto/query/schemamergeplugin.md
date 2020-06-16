---
title: schema_merge 플러그 인-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 schema_merge 플러그 인을 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/16/2020
ms.openlocfilehash: b1f3ef10ac5cee3eb9bc1c1dca4c0de26bd85477
ms.sourcegitcommit: 8e097319ea989661e1958efaa1586459d2b69292
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/15/2020
ms.locfileid: "84780204"
---
# <a name="schema_merge-plugin"></a>schema_merge 플러그 인

테이블 형식 스키마 정의를 통합 스키마에 병합 합니다. 

스키마 정의는 연산자에 의해 생성 되는 형식 이어야 [`getschema`](./getschemaoperator.md) 합니다.

`schema merge`작업은 입력 스키마의 열을 조인 하 고 데이터 형식을 일반적인 형식으로 줄이려고 시도 합니다. 데이터 형식을 줄일 수 없는 경우 문제가 있는 열에 오류가 표시 됩니다.

```kusto
let Schema1=Table1 | getschema;
let Schema2=Table2 | getschema;
union Schema1, Schema2 | evaluate schema_merge()
```

**구문**

`T``|` `evaluate` `schema_merge(` *PreserveOrder*`)`

**인수**

* *PreserveOrder*: (선택 사항)로 설정 하면 `true` 유지 되는 첫 번째 테이블 형식 스키마에 정의 된 대로 플러그 인에서 열 순서의 유효성을 검사 하도록 지시 합니다. 동일한 열이 여러 스키마에 있는 경우 열 서 수는 표시 된 첫 번째 스키마의 열 서 수와 같아야 합니다. 기본값은 `true`입니다.

**반환**

`schema_merge`플러그 인은 반환 되는 연산자와 유사한 출력을 반환 [`getschema`](./getschemaoperator.md) 합니다.

**예**

새 열이 추가 된 스키마와 병합 합니다.

```kusto
let schema1 = datatable(Uri:string, HttpStatus:int)[] | getschema;
let schema2 = datatable(Uri:string, HttpStatus:int, Referrer:string)[] | getschema;
union schema1, schema2 | evaluate schema_merge()
```

*만들어집니다*

|ColumnName | ColumnOrdinal | DataType | ColumnType|
|---|---|---|---|
|URI|0|System.String|문자열|
|HttpStatus|1|System.Int32|int|
|Referrer|2|System.String|문자열|

열 순서가 다른 스키마와 병합 `HttpStatus` 합니다 (새 변형의에서로의 서 수 변경 `1` `2` ).

```kusto
let schema1 = datatable(Uri:string, HttpStatus:int)[] | getschema;
let schema2 = datatable(Uri:string, Referrer:string, HttpStatus:int)[] | getschema;
union schema1, schema2 | evaluate schema_merge()
```

*만들어집니다*

|ColumnName | ColumnOrdinal | DataType | ColumnType|
|---|---|---|---|
|URI|0|System.String|문자열|
|Referrer|1|System.String|문자열|
|HttpStatus|-1|오류 (알 수 없는 CSL 유형: 오류 (열 순서가 잘못 됨))|오류 (열 순서가 잘못 됨)|

다른 열 순서를 사용 하지만가로 설정 된 스키마와 병합 `PreserveOrder` `false` 합니다.

```kusto
let schema1 = datatable(Uri:string, HttpStatus:int)[] | getschema;
let schema2 = datatable(Uri:string, Referrer:string, HttpStatus:int)[] | getschema;
union schema1, schema2 | evaluate schema_merge(PreserveOrder = false)
```

*만들어집니다*

|ColumnName | ColumnOrdinal | DataType | ColumnType|
|---|---|---|---|
|URI|0|System.String|문자열
|Referrer|1|System.String|문자열
|HttpStatus|2|System.Int32|int|
