---
title: SQL로 데이터 내보내기 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기에서 SQL로 데이터 내보내기에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 7deeb3868800f00a828eb09cc605e4d7e5227032
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81521627"
---
# <a name="export-data-to-sql"></a>데이터를 SQL로 내보내기

데이터를 SQL로 내보내면 쿼리를 실행하고 Azure SQL Database 서비스에서 호스팅하는 SQL 데이터베이스와 같은 SQL 데이터베이스의 테이블로 결과를 보낼 수 있습니다.

**구문**

`.export`[`async` `to` `sql` ] *SqlTableName* *SqlConnectionString* [`with` `(` *속성 이름* `=` *속성값*`,`... `)`] `<|` *쿼리*

위치:
* *비동기*: 명령은 비동기 모드에서 실행됩니다 (선택 사항).
* *SqlTable Name* 데이터가 삽입되는 SQL 데이터베이스 테이블 이름입니다.
  주입 공격으로부터 보호하기 위해 이 이름은 제한됩니다.
* *SqlConnectionString은* `string` `ADO.NET` 연결 문자열 형식을 따르고 연결하는 SQL 끝점 및 데이터베이스를 설명하는 리터럴입니다. 보안상의 이유로 연결 문자열이 제한됩니다.
* *PropertyName*, *PropertyValue는* 이름 (식별자) 및 값 (문자열 리터럴)의 쌍입니다.

속성

|이름               |값           |Description|
|-------------------|-----------------|-----------|
|`firetriggers`     |`true` 또는 `false`|대상 `true`시스템에 SQL 테이블에 정의된 INSERT 트리거를 발생하도록 지시하는 경우 기본값은 `false`입니다. (자세한 내용은 [대량 삽입](https://msdn.microsoft.com/library/ms188365.aspx) 및 [System.Data.SqlClient.SqlBulkCopy](https://msdn.microsoft.com/library/system.data.sqlclient.sqlbulkcopy(v=vs.110).aspx)참조)|
|`createifnotexists`|`true` 또는 `false`|대상 `true`SQL 테이블이 아직 존재하지 않는 경우 . 이 `primarykey` 경우 기본 키인 결과 열을 나타내려면 속성을 제공해야 합니다. 기본값은 `false`입니다.|
|`primarykey`       |                 |`true`이 `createifnotexists` 명령에서 만든 경우 SQL 테이블의 기본 키로 사용되는 결과의 열 이름을 나타냅니다.|
|`persistDetails`   |`bool`           |명령이 결과를 유지해야 한다는 것을 `async` 나타냅니다(플래그 참조). `true` 비동기에서 실행되는 기본값이지만 호출자가 결과를 필요로 하지 않는 경우 해제할 수 있습니다. 기본값은 `false` 동기 실행이지만 켜질 수 있습니다. |
|`token`            |`string`         |Kusto가 인증을 위해 SQL 끝점으로 전달하는 AAD 액세스 토큰입니다. 설정하면 SQL 연결 문자열에 와 같은 `Authentication`인증 `User ID`정보가 `Password`포함되어서는 안 됩니다.|

## <a name="limitations-and-restrictions"></a>제한 사항

SQL 데이터베이스로 데이터를 내보낼 때에는 여러 가지 제한 사항과 제한 사항이 있습니다.

1. Kusto는 클라우드 서비스이므로 연결 문자열은 클라우드에서 액세스할 수 있는 데이터베이스를 가리커야 합니다. 특히 퍼블릭 클라우드에서 액세스할 수 없으므로 온-프레미스 데이터베이스로 내보낼 수 없습니다.

2. Kusto는 호출 주체가 Azure Active Directory 보안 주체(또는)인`aaduser=` `aadapp=`경우 Active Directory 통합 인증을 지원합니다.
   또는 Kusto는 연결 문자열의 일부로 SQL 데이터베이스에 대한 자격 증명을 제공하는 것도 지원합니다. 다른 인증 방법은 지원되지 않습니다. SQL 데이터베이스에 표시되는 ID는 항상 Kusto 서비스 ID 자체가 아닌 명령 호출자에서 방출됩니다.

3. SQL 데이터베이스의 대상 테이블이 있는 경우 쿼리 결과 스키마와 일치해야 합니다. 경우에 따라 (예: Azure SQL Database) 즉, 테이블에 ID 열로 표시된 하나의 열이 있습니다.

4. 대용량 데이터를 내보내는 데 시간이 오래 걸릴 수 있습니다. 대량 가져오기 중에 최소한의 로깅을 위해 대상 SQL 테이블을 설정하는 것이 좋습니다.
   [대량 가져오기 및 데이터 내보내기에 > 데이터베이스 기능을 > SQL Server 데이터베이스 엔진 >](https://msdn.microsoft.com/library/ms190422.aspx)참조하십시오.

5. 데이터 내보내기는 SQL 대량 복사본을 사용하여 수행되며 대상 SQL 데이터베이스에 대한 트랜잭션 보장을 제공하지 않습니다. 자세한 내용은 [트랜잭션 및 대량 복사 작업을](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/transaction-and-bulk-copy-operations) 참조하십시오.

6. SQL 테이블 이름은 문자, 숫자, 공백, 밑줄 (점)`_``.`및 하이픈 ()로`-`구성된 이름으로 제한됩니다.

7. SQL 연결 문자열은 다음과 `Persist Security Info` 같이 제한됩니다. `false` `Encrypt` `true` `Trust Server Certificate` `false`

8. 새 SQL 테이블을 만들 때 열의 기본 키 속성을 지정할 수 있습니다. 열이 형식인 `string`경우 SQL은 기본 키 열의 다른 제한 사항으로 인해 테이블 만들기를 거부할 수 있습니다. 해결 방법은 데이터를 내보내기 전에 SQL에서 테이블을 수동으로 만드는 것입니다. 이러한 제한의 이유는 SQL의 기본 키 열의 크기가 무제한일 수는 없지만 Kusto 테이블 열에는 선언된 크기 제한이 없기 때문입니다.

## <a name="azure-db-aad-integrated-authentication-documentation"></a>Azure DB AAD 통합 인증 문서

* [SQL 데이터베이스를 사용하여 인증하기 위해 Azure Active Directory 인증 사용](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication)
* [Azure SQL DB 및 SQL DW 도구에 대한 Azure AD 인증 확장] (https://azure.microsoft.com/blog/azure-ad-authentication-extensions-for-azure-sql-db-and-sql-dw-tools/)

**예** 

이 예제에서 Kusto는 쿼리를 실행한 다음 쿼리에서 생성된 `MySqlTable` 첫 번째 `MyDatabase` 레코드 `myserver`집합을 서버의 데이터베이스의 테이블로 내보냅니다.

```kusto 
.export async to sql MySqlTable
    h@"Server=tcp:myserver.database.windows.net,1433;Database=MyDatabase;Authentication=Active Directory Integrated;Connection Timeout=30;"
    <| print Id="d3b68d12-cbd3-428b-807f-2c740f561989", Name="YSO4", DateOfBirth=datetime(2017-10-15)
```

이 예제에서 Kusto는 쿼리를 실행한 다음 쿼리에서 생성된 `MySqlTable` 첫 번째 `MyDatabase` 레코드 `myserver`집합을 서버의 데이터베이스의 테이블로 내보냅니다.
대상 테이블이 대상 데이터베이스에 없으면 생성됩니다.

```kusto 
.export async to sql ['dbo.MySqlTable']
    h@"Server=tcp:myserver.database.windows.net,1433;Database=MyDatabase;Authentication=Active Directory Integrated;Connection Timeout=30;"
    with (createifnotexists="true", primarykey="Id")
    <| print Message = "Hello World!", Timestamp = now(), Id=12345678
```