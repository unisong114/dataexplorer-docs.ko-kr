---
title: 외부 테이블 제어 명령-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 외부 테이블 제어 명령을 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: 580f675360b96d56d43e1100cbba97d09a95c945
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83227709"
---
# <a name="external-table-control-commands"></a>외부 테이블 제어 명령

외부 테이블에 대 한 개요는 [외부 테이블](../query/schema-entities/externaltables.md) 을 참조 하세요. 

## <a name="common-external-tables-control-commands"></a>일반적인 외부 테이블 제어 명령

다음 _명령은 모든 형식의 외부 테이블과_ 관련이 있습니다.

## <a name="show-external-tables"></a>. 외부 테이블 표시

* 데이터베이스 (또는 특정 외부 테이블)의 모든 외부 테이블을 반환 합니다.
* [데이터베이스 모니터 권한이](../management/access-control/role-based-authorization.md)필요 합니다.

**구문:** 

`.show` `external` `tables`

`.show``external` `table` *TableName*

**출력**

| 출력 매개 변수 | 유형   | 설명                                                         |
|------------------|--------|---------------------------------------------------------------------|
| TableName        | 문자열 | 외부 테이블의 이름                                             |
| TableType        | 문자열 | 외부 테이블의 유형입니다.                                              |
| 폴더           | 문자열 | 테이블의 폴더                                                     |
| DocString        | 문자열 | 테이블을 문서화 하는 문자열                                       |
| 속성       | 문자열 | 테이블의 JSON 직렬화 된 속성 (테이블 형식에만 해당) |


**예:**

```kusto
.show external tables
.show external table T
```

| TableName | TableType | 폴더         | DocString | 속성 |
|-----------|-----------|----------------|-----------|------------|
| T         | Blob      | ExternalTables | Docs      | {}         |


## <a name="show-external-table-schema"></a>. 외부 테이블 스키마 표시

* 외부 테이블의 스키마를 JSON 또는 CSL로 반환 합니다. 
* [데이터베이스 모니터 권한이](../management/access-control/role-based-authorization.md)필요 합니다.

**구문:** 

`.show``external` `table` *TableName* `schema` `as` ( `json`  |  `csl` )

`.show``external` `table` *TableName*`cslschema`

**출력**

| 출력 매개 변수 | 유형   | 설명                        |
|------------------|--------|------------------------------------|
| TableName        | 문자열 | 외부 테이블의 이름            |
| 스키마           | 문자열 | JSON 형식의 테이블 스키마 |
| DatabaseName     | 문자열 | 테이블의 데이터베이스 이름             |
| 폴더           | 문자열 | 테이블의 폴더                    |
| DocString        | 문자열 | 테이블을 문서화 하는 문자열      |

**예:**

```kusto
.show external table T schema as JSON
```

```kusto
.show external table T schema as CSL
.show external table T cslschema
```

**출력**

*n*

| TableName | 스키마    | DatabaseName | 폴더         | DocString |
|-----------|----------------------------------|--------------|----------------|-----------|
| T         | {"Name": "ExternalBlob",<br>"폴더": "ExternalTables",<br>"DocString": "Docs",<br>"OrderedColumns": [{"Name": "x", "Type": "system.string", "CslType": "long", "DocString": ""}, {"Name": "s", "Type": "system.string", "CslType": "String", "DocString": ""}]} | DB           | ExternalTables | Docs      |


*csl:*

| TableName | 스키마          | DatabaseName | 폴더         | DocString |
|-----------|-----------------|--------------|----------------|-----------|
| T         | x:long, 토 문자열 | DB           | ExternalTables | Docs      |

## <a name="drop-external-table"></a>. drop external table

* 외부 테이블을 삭제 합니다. 
* 이 작업을 수행 하면 외부 테이블 정의를 복원할 수 없습니다.
* [데이터베이스 관리자 권한이](../management/access-control/role-based-authorization.md)필요 합니다.

**구문:**  

`.drop``external` `table` *TableName*

**출력**

삭제 된 테이블의 속성을 반환 합니다. 을 참조 하십시오 [. 외부 테이블을 표시](#show-external-tables)합니다.

**예:**

```kusto
.drop external table ExternalBlob
```

| TableName | TableType | 폴더         | DocString | 스키마       | 속성 |
|-----------|-----------|----------------|-----------|-----------------------------------------------------|------------|
| T         | Blob      | ExternalTables | Docs      | [{"Name": "x", "CslType": "long"},<br> {"Name": "s", "CslType": "string"}] | {}         |

