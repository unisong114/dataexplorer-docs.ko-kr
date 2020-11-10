---
title: mysql_request 플러그 인-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 mysql_request 플러그 인을 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: c44935a98110cd47f2a40bb261659e12627860c0
ms.sourcegitcommit: 25c0440cb0390b9629b819611844f1375de00a66
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94422130"
---
# <a name="mysql_request-plugin-preview"></a>mysql_request 플러그 인 (미리 보기)

::: zone pivot="azuredataexplorer"

`mysql_request`플러그 인은 MySQL 서버 네트워크 끝점에 SQL 쿼리를 보내고 결과에서 첫 번째 행 집합을 반환 합니다. 이 쿼리는 행 집합을 두 개 이상 반환할 수 있지만, 첫 번째 행 집합만 Kusto 쿼리의 나머지 부분에 사용할 수 있습니다.

> [!IMPORTANT]
> `mysql_request`플러그 인은 미리 보기 모드 이며 기본적으로 사용 하지 않도록 설정 되어 있습니다.
> 플러그 인을 사용 하도록 설정 하려면 [ `.enable plugin mysql_request` 명령을](../management/enable-plugin.md)실행 합니다. 활성화 된 플러그 인을 확인 하려면를 사용 [합니다. 플러그 인 관리 명령 표시](../management/show-plugins.md)

## <a name="syntax"></a>구문

`evaluate``mysql_request` `(` *ConnectionString* `,` *SqlQuery* [ `,` *sqlparameters* ]`)`

## <a name="arguments"></a>인수

Name | 유형 | Description | 필수/선택 |
---|---|---|---
| *ConnectionString* | `string` literal | MySQL 서버 네트워크 끝점을 가리키는 연결 문자열을 나타냅니다. [인증](#username-and-password-authentication) 및 [네트워크 끝점](#specify-the-network-endpoint)을 지정 하는 방법을 참조 하세요. | 필수 |
| *SqlQuery* | `string` literal | SQL 끝점에 대해 실행할 쿼리를 나타냅니다. 는 하나 이상의 행 집합을 반환 해야 하지만, 첫 번째 행 집합은 Kusto 쿼리의 나머지 부분에서 사용할 수 있습니다. | 필수|
| *Sqlparameter* | 형식의 상수 값 `dynamic` | 쿼리와 함께 매개 변수로 전달할 키-값 쌍을 보유 합니다. | 선택 사항 |

## <a name="set-callout-policy"></a>콜아웃 정책 설정

플러그 인은 MySql DB에 대 한 설명선을 만듭니다. 클러스터의 [콜아웃 정책이](../management/calloutpolicy.md) 대상 MySqlDbUri에 대 한 형식의 호출을 활성화 하는지 확인 `mysql` 합니다. *MySqlDbUri*

다음 예에서는 MySQL DB에 대 한 콜아웃 정책을 정의 하는 방법을 보여 줍니다. 콜아웃 정책을 특정 끝점 (,)으로 제한 하는 것이 좋습니다 `my_endpoint1` `my_endpoint2` .

```kusto
[
  {
    "CalloutType": "mysql",
    "CalloutUriRegex": "my_endpoint1\\.mysql\\.database\\.azure\\.com",
    "CanCall": true
  },
  {
    "CalloutType": "mysql",
    "CalloutUriRegex": "my_endpoint2\\.mysql\\.database\\.azure\\.com",
    "CanCall": true
  }
]
```

다음 예에서는 `mysql` *callouttype* 에 대 한 alter 콜아웃 정책 명령을 보여 줍니다.

```kusto
.alter cluster policy callout @'[{"CalloutType": "mysql", "CalloutUriRegex": "\\.mysql\\.database\\.azure\\.com", "CanCall": true}]'
```

## <a name="username-and-password-authentication"></a>사용자 이름 및 암호 인증

Mysql_request 플러그 인은 MySQL 서버 끝점에 대 한 사용자 이름 및 암호 인증만 지원 하며 Azure Active Directory 인증과 통합 되지 않습니다. 

사용자 이름 및 암호는 다음 매개 변수를 사용 하 여 연결 문자열의 일부로 제공 됩니다.

`User ID=...; Password=...;`
    
> [!WARNING]
> 기밀 또는 보호 된 정보는 모든 Kusto 추적에서 생략 되도록 연결 문자열 및 쿼리에서 난독 처리 되어야 합니다. 자세한 내용은 난독 처리 되는 [문자열 리터럴](scalar-data-types/string.md#obfuscated-string-literals)을 참조 하세요.

## <a name="encryption-and-server-validation"></a>암호화 및 서버 유효성 검사

보안상의 이유로 `SslMode` `Required` MySQL 서버 네트워크 끝점에 연결할 때는 무조건로 설정 됩니다. 따라서 유효한 SSL/TLS 서버 인증서를 사용 하 여 SQL Server를 구성 해야 합니다.

## <a name="specify-the-network-endpoint"></a>네트워크 끝점 지정

SQL 네트워크 끝점을 연결 문자열의 일부로 지정 합니다.

**구문** :

`Server``=` *FQDN* [ `Port` `=` *포트* ]

위치:

* *FQDN* 은 끝점의 정규화 된 도메인 이름입니다.
* *포트* 는 끝점의 TCP 포트입니다. 기본적으로 `3306` 는로 가정 합니다.

## <a name="examples"></a>예제


### <a name="sql-query-to-azure-mysql-db"></a>Azure MySQL DB에 대 한 SQL 쿼리

다음 예에서는 Azure MySQL DB 데이터베이스에 SQL 쿼리를 보냅니다. 에서 모든 레코드를 검색 한 `[dbo].[Table]` 다음 결과를 처리 합니다.

> [!NOTE]
> 이 방법을 사용 하 여 데이터를 필터링 하거나 프로젝트를 수행 하는 것이 좋습니다. Kusto 최적화 프로그램은 현재 Kusto와 SQL 간의 쿼리를 최적화 하려고 시도 하지 않으므로 가능한 가장 작은 데이터 집합을 반환 하도록 SQL 쿼리를 생성 해야 합니다.

```kusto
evaluate sql_request(
    'Server=contoso.mysql.database.azure.com; Port = 3306;'
    'Database=Fabrikam;'
    h'UID=USERNAME;'
    h'Pwd=PASSWORD;', 
  'select * from [dbo].[Table]')
| where Id > 0
| project Name
```

### <a name="sql-authentication-with-username-and-password"></a>사용자 이름 및 암호를 사용 하는 SQL 인증

다음 예제는 이전 예제와 동일 하지만 SQL 인증은 사용자 이름 및 암호를 통해 수행 됩니다. 기밀성을 위해 난독 처리 되는 문자열을 사용 합니다.

```kusto
evaluate sql_request(
   'Server=contoso.mysql.database.azure.com; Port = 3306;'
    'Database=Fabrikam;'
    h'UID=USERNAME;'
    h'Pwd=PASSWORD;', 
  'select * from [dbo].[Table]')
| where Id > 0
| project Name
```

### <a name="sql-query-to-azure-sql-db-with-modifications"></a>수정이 있는 Azure SQL DB에 대 한 SQL 쿼리

다음 예에서는에서 모든 레코드를 검색 하 `[dbo].[Table]` 는 동안 다른 `datetime` 열을 추가한 다음 Kusto 쪽에서 결과를 처리 하는 AZURE sql DB 데이터베이스에 sql 쿼리를 보냅니다.
SQL 쿼리에 사용할 SQL 매개 변수 ()를 지정 합니다 `@param0` .

```kusto
evaluate mysql_request(
  'Server=contoso.mysql.database.azure.com; Port = 3306;'
    'Database=Fabrikam;'
    h'UID=USERNAME;'
    h'Pwd=PASSWORD;', 
  'select *, @param0 as dt from [dbo].[Table]',
  dynamic({'param0': datetime(2020-01-01 16:47:26.7423305)}))
| where Id > 0
| project Name
```

::: zone-end

::: zone pivot="azuremonitor"

이 기능은에서 지원 되지 않습니다 Azure Monitor

::: zone-end
