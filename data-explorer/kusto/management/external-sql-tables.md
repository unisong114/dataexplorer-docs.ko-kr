---
title: 외부 SQL 테이블 만들기 및 변경-Azure 데이터 탐색기
description: 이 문서에서는 외부 SQL 테이블을 만들고 변경 하는 방법을 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: d6473fd0efd588bfc7a6990dd2f57d70960b40e2
ms.sourcegitcommit: 898f67b83ae8cf55e93ce172a6fd3473b7c1c094
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/21/2020
ms.locfileid: "92342759"
---
# <a name="create-and-alter-external-sql-tables"></a>외부 SQL 테이블 만들기 및 변경

명령이 실행 되는 데이터베이스에서 외부 SQL 테이블을 만들거나 변경 합니다.  

## <a name="syntax"></a>구문

( `.create`  |  `.alter`  |  `.create-or-alter` ) `external` `table` *TableName* ([columnName: columnType], ...)  
`kind` `=` `sql`  
`table``=` *Sqltablename*  
`(`*SqlServerConnectionString*`)`  
[ `with` `(` [ `docstring` `=` *설명서*] [ `,` `folder` `=` *FolderName*], *property_name* `=` *값*... `,` `)` ]

## <a name="parameters"></a>매개 변수

* *TableName* -외부 테이블 이름입니다. [엔터티 이름](../query/schema-entities/entity-names.md)규칙을 따라야 합니다. 외부 테이블은 동일한 데이터베이스의 일반 테이블과 동일한 이름을 가질 수 없습니다.
* *Sqltablename* -SQL 테이블의 이름입니다.
* *SqlServerConnectionString* -SQL server에 대 한 연결 문자열입니다. 다음 방법 중 하나를 사용할 수 있습니다. 
  * *Aad 통합 인증* ( `Authentication="Active Directory Integrated"` ): 사용자 또는 응용 프로그램은 Aad를 통해 kusto를 인증 하 고, 동일한 토큰을 사용 하 여 SQL Server 네트워크 끝점에 액세스 합니다.
  * *사용자 이름/암호 인증* ( `User ID=...; Password=...;` ) [연속 내보내기](data-export/continuous-data-export.md)에 외부 테이블을 사용 하는 경우이 메서드를 사용 하 여 인증을 수행 해야 합니다. 

> [!WARNING]
> 기밀 정보를 포함 하는 연결 문자열 및 쿼리는 모든 Kusto 추적에서 생략 되도록 난독 처리 되어야 합니다. 자세한 내용은 난독 처리 되는 [문자열 리터럴](../query/scalar-data-types/string.md#obfuscated-string-literals)을 참조 하세요.

## <a name="optional-properties"></a>선택적 속성

| 속성            | 유형            | 설명                          |
|---------------------|-----------------|---------------------------------------------------------------------------------------------------|
| `folder`            | `string`        | 테이블의 폴더입니다.                  |
| `docString`         | `string`        | 테이블을 문서화 하는 문자열입니다.      |
| `firetriggers`      | `true`/`false`  | 인 경우 `true` 대상 시스템에서 SQL 테이블에 정의 된 삽입 트리거를 발생 시 정하도록 지시 합니다. 기본값은 `false`입니다. 자세한 내용은 [BULK INSERT](/sql/t-sql/statements/bulk-insert-transact-sql) 및 [SqlBulkCopy](/dotnet/api/system.data.sqlclient.sqlbulkcopy)를 참조 하세요. |
| `createifnotexists` | `true`/ `false` | 인 경우 `true` 대상 SQL 테이블이 아직 없으면 생성 됩니다. `primarykey` 이 경우 기본 키 인 결과 열을 표시 하려면 속성을 제공 해야 합니다. 기본값은 `false`입니다.  |
| `primarykey`        | `string`        | `createifnotexists`이 이면 `true` 결과 열 이름이이 명령으로 생성 된 경우 SQL 테이블의 기본 키로 사용 됩니다.                  |

> [!NOTE]
> * 테이블이 있으면 `.create` 오류가 발생 하 여 명령이 실패 합니다. `.create-or-alter` `.alter` 기존 테이블을 수정 하려면 또는를 사용 합니다. 
> * 외부 SQL 테이블의 스키마 또는 형식 변경은 지원 되지 않습니다. 

에 대 한 [데이터베이스 사용자 권한](../management/access-control/role-based-authorization.md) `.create` 및에 대 한 [테이블 관리자 권한이](../management/access-control/role-based-authorization.md) 필요 `.alter` 합니다. 
 
**예제** 

```kusto
.create external table MySqlExternalTable (x:long, s:string) 
kind=sql
table=MySqlTable
( 
   h@'Server=tcp:myserver.database.windows.net,1433;Authentication=Active Directory Integrated;Initial Catalog=mydatabase;'
)
with 
(
   docstring = "Docs",
   folder = "ExternalTables", 
   createifnotexists = true,
   primarykey = x,
   firetriggers=true
)  
```

**출력**

| TableName   | TableType | 폴더         | DocString | 속성                            |
|-------------|-----------|----------------|-----------|---------------------------------------|
| MySqlExternalTable | Sql       | ExternalTables | Docs      | {<br>  "TargetEntityKind": "sqltable '",<br>  "TargetEntityName": "MySqlTable",<br>  "TargetEntityConnectionString": "Server = tcp: myserver. net.tcp, 1433; Authentication = Active Directory Integrated; 초기 카탈로그 = mydatabase; ",<br>  "FireTriggers": true,<br>  "CreateIfNotExists": true,<br>  "PrimaryKey": "x"<br>} |

## <a name="querying-an-external-table-of-type-sql"></a>SQL 유형의 외부 테이블 쿼리

외부 SQL 테이블 쿼리는 지원 됩니다. [외부 테이블 쿼리](../../data-lake-query-data.md)를 참조 하세요. 

> [!Note]
> SQL external table 쿼리 구현은 문을 실행 합니다 `SELECT x, s FROM MySqlTable` . 여기서 `x` 및 `s` 는 외부 테이블 열 이름입니다. 쿼리의 나머지 부분은 Kusto 쪽에서 실행 됩니다.

다음 외부 테이블 쿼리를 살펴보십시오. 

```kusto
external_table('MySqlExternalTable') | count
```

Kusto는 SQL database에 대 한 쿼리를 실행 한 `SELECT x, s FROM MySqlTable` 다음, kusto side의 수를 계산 합니다. 이러한 경우에는 외부 테이블 함수를 사용 하는 대신 T-sql에서 직접 작성 하 `SELECT COUNT(1) FROM MySqlTable` 고 [sql_request 플러그 인](../query/sqlrequestplugin.md)을 사용 하 여 실행 하는 경우 성능이 더 좋을 것으로 예상 됩니다. 마찬가지로 필터는 SQL 쿼리로 푸시되 지 않습니다.  

외부 테이블을 사용 하 여 쿼리에서 전체 테이블 (또는 관련 열)을 읽어야 할 때 Kusto side에서 추가 실행을 위해 SQL 테이블을 쿼리할 수 있습니다. SQL 쿼리를 T-sql에서 최적화할 수 있는 경우 [sql_request 플러그 인](../query/sqlrequestplugin.md)을 사용 합니다.

## <a name="next-steps"></a>다음 단계

* [외부 테이블 일반 제어 명령](./external-table-commands.md)
* [Azure Storage 또는 Azure Data Lake의 외부 테이블 만들기 및 변경](external-tables-azurestorage-azuredatalake.md)