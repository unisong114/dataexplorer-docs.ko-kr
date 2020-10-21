---
title: SQL로 데이터 내보내기-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기에서 SQL로 데이터를 내보내는 방법을 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 61c65e2667356f2b2ee9e53c3dd1c210e84c72f8
ms.sourcegitcommit: 898f67b83ae8cf55e93ce172a6fd3473b7c1c094
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/21/2020
ms.locfileid: "92342810"
---
# <a name="export-data-to-sql"></a>SQL로 데이터 내보내기

SQL로 데이터 내보내기를 사용 하면 쿼리를 실행 하 고 해당 결과를 SQL 데이터베이스의 테이블 (예: Azure SQL Database 서비스에서 호스트 되는 SQL 데이터베이스)에 보낼 수 있습니다.

**구문**

`.export`[ `async` ] `to` `sql` *Sqltablename* *SqlConnectionString* [ `with` `(` *PropertyName* `=` *PropertyValue* `,` ... `)` ] `<|` *쿼리*

위치:
* *async*: 명령이 비동기 모드로 실행 됩니다 (선택 사항).
* *Sqltablename* 데이터가 삽입 되는 SQL database 테이블 이름입니다.
  삽입 공격 으로부터 보호 하기 위해이 이름은 제한 됩니다.
* *SqlConnectionString* 는 `string` `ADO.NET` 연결 문자열 형식을 따르며 연결 하는 SQL 끝점과 데이터베이스를 설명 하는 리터럴입니다. 보안상의 이유로 연결 문자열이 제한 됩니다.
* *PropertyName*, *PropertyValue* 는 이름 (식별자)의 쌍 및 값 (문자열 리터럴)입니다.

속성:

|Name               |값           |설명|
|-------------------|-----------------|-----------|
|`firetriggers`     |`true` 또는 `false`|인 경우 `true` 대상 시스템에서 SQL 테이블에 정의 된 삽입 트리거를 발생 시 정하도록 지시 합니다. 기본값은 `false`입니다. 자세한 내용은 [BULK INSERT](/sql/t-sql/statements/bulk-insert-transact-sql) 및 [SqlBulkCopy](/dotnet/api/system.data.sqlclient.sqlbulkcopy)를 참조 하세요.|
|`createifnotexists`|`true` 또는 `false`|인 경우 `true` 대상 SQL 테이블이 없는 경우 해당 테이블을 만듭니다. `primarykey` 이 경우 기본 키 인 결과 열을 표시 하려면 속성을 제공 해야 합니다. 기본값은 `false`입니다.|
|`primarykey`       |                 |`createifnotexists`가 인 경우 `true` 이 명령으로 생성 되는 경우 SQL 테이블의 기본 키로 사용 될 결과의 열 이름을 나타냅니다.|
|`persistDetails`   |`bool`           |명령 결과를 유지 해야 함을 나타냅니다 (플래그 참조 `async` ). 는 `true` 비동기 실행에서로 기본 설정 되지만 호출자가 결과를 요구 하지 않는 경우 해제할 수 있습니다. `false`는 동기 실행에서로 기본 설정 되지만 켤 수 있습니다. |
|`token`            |`string`         |인증을 위해 Kusto가 SQL 끝점으로 전달 하는 AAD 액세스 토큰입니다. 설정 하는 경우 SQL 연결 문자열은, 또는와 같은 인증 정보를 포함 하지 않아야 합니다 `Authentication` `User ID` `Password` .|

## <a name="limitations-and-restrictions"></a>제한 사항

SQL database로 데이터를 내보내는 경우 다음과 같은 몇 가지 제한 사항이 있습니다.

1. Kusto는 클라우드 서비스 이므로 연결 문자열은 클라우드에서 액세스할 수 있는 데이터베이스를 가리켜야 합니다. 특히 공용 클라우드에서 액세스할 수 없기 때문에 온-프레미스 데이터베이스로 내보낼 수 없습니다.

2. Kusto는 호출 보안 주체가 Azure Active Directory 보안 주체 (또는) 인 경우 Active Directory 통합 인증을 지원 `aaduser=` `aadapp=` 합니다.
   또는 Kusto는 연결 문자열의 일부로 SQL database에 대 한 자격 증명을 제공 하는 기능도 지원 합니다. 다른 인증 방법이 지원 되지 않습니다. SQL 데이터베이스에 표시 되는 id는 항상 Kusto 서비스 id 자체가 아닌 명령 호출자에서 emanates 합니다.

3. SQL database의 대상 테이블이 있으면 쿼리 결과 스키마와 일치 해야 합니다. 일부 경우에는 (예: Azure SQL Database) 테이블에 id 열로 표시 된 열이 하나 있음을 의미 합니다.

4. 대량의 데이터를 내보내는 데 시간이 오래 걸릴 수 있습니다. 대량 가져오기 작업을 수행 하는 동안 최소 로깅에 대해 대상 SQL 테이블을 설정 하는 것이 좋습니다.
   [데이터 대량 가져오기 및 내보내기 > SQL Server 데이터베이스 엔진 > ... > 데이터베이스 기능](/sql/relational-databases/import-export/prerequisites-for-minimal-logging-in-bulk-import)을 참조 하세요.

5. 데이터 내보내기는 SQL 대량 복사를 사용 하 여 수행 되며 대상 SQL 데이터베이스의 트랜잭션 보장을 제공 하지 않습니다. 자세한 내용은 [트랜잭션 및 대량 복사 작업](/dotnet/framework/data/adonet/sql/transaction-and-bulk-copy-operations) 을 참조 하세요.

6. SQL 테이블 이름은 문자, 숫자, 공백, 밑줄 ( `_` ), 점 ( `.` ) 및 하이픈 ()으로 구성 된 이름으로 제한 됩니다 `-` .

7. SQL 연결 문자열은 다음과 같이 제한 됩니다. `Persist Security Info` 은 명시적으로로 설정 되 고, `false` `Encrypt` 는로 설정 `true` 되며, `Trust Server Certificate` 는로 설정 `false` 됩니다.

8. 새 SQL 테이블을 만들 때 열의 기본 키 속성을 지정할 수 있습니다. 열이 형식인 경우 SQL은 `string` 기본 키 열에 대 한 다른 제한 사항으로 인해 테이블 만들기를 거부할 수 있습니다. 해결 방법은 데이터를 내보내기 전에 SQL에서 테이블을 수동으로 만드는 것입니다. 이러한 제한이 적용 되는 이유는 SQL의 기본 키 열 크기는 제한 되지 않지만 Kusto 테이블 열에는 선언 된 크기 제한이 없기 때문입니다.

## <a name="azure-db-aad-integrated-authentication-documentation"></a>Azure DB AAD 통합 인증 설명서

* [SQL Database 인증에 Azure Active Directory 인증 사용](/azure/sql-database/sql-database-aad-authentication)
* [Azure SQL DB 및 SQL DW tools 용 azure AD 인증 확장] (https://azure.microsoft.com/blog/azure-ad-authentication-extensions-for-azure-sql-db-and-sql-dw-tools/)

**예** 

이 예에서 Kusto는 쿼리를 실행 한 다음 쿼리에서 생성 된 첫 번째 레코드 집합을 `MySqlTable` 서버에 있는 데이터베이스의 테이블에 내보냅니다 `MyDatabase` `myserver` .

```kusto 
.export async to sql MySqlTable
    h@"Server=tcp:myserver.database.windows.net,1433;Database=MyDatabase;Authentication=Active Directory Integrated;Connection Timeout=30;"
    <| print Id="d3b68d12-cbd3-428b-807f-2c740f561989", Name="YSO4", DateOfBirth=datetime(2017-10-15)
```

이 예에서 Kusto는 쿼리를 실행 한 다음 쿼리에서 생성 된 첫 번째 레코드 집합을 `MySqlTable` 서버에 있는 데이터베이스의 테이블에 내보냅니다 `MyDatabase` `myserver` .
대상 테이블이 대상 데이터베이스에 존재 하지 않는 경우 만들어집니다.

```kusto 
.export async to sql ['dbo.MySqlTable']
    h@"Server=tcp:myserver.database.windows.net,1433;Database=MyDatabase;Authentication=Active Directory Integrated;Connection Timeout=30;"
    with (createifnotexists="true", primarykey="Id")
    <| print Message = "Hello World!", Timestamp = now(), Id=12345678
```