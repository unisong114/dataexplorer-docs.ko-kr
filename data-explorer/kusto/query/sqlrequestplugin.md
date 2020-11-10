---
title: sql_request 플러그 인-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 sql_request 플러그 인을 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: a8a0aae8732104ee64630c1fddb4d563cb542351
ms.sourcegitcommit: 25c0440cb0390b9629b819611844f1375de00a66
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94417560"
---
# <a name="sql_request-plugin"></a>sql_request 플러그 인

::: zone pivot="azuredataexplorer"

`sql_request`플러그 인은 SQL Server 네트워크 끝점으로 SQL 쿼리를 보내고 결과에서 첫 번째 행 집합을 반환 합니다.
이 쿼리는 행 집합을 두 개 이상 반환할 수 있지만, 첫 번째 행 집합만 Kusto 쿼리의 나머지 부분에 사용할 수 있습니다.

## <a name="syntax"></a>구문

  `evaluate``sql_request` `(` *ConnectionString* `,` *SqlQuery* [ `,` *sqlparameters* [ `,` *Options* ]]`)`

## <a name="arguments"></a>인수

* *ConnectionString* : `string` SQL Server 네트워크 끝점을 가리키는 연결 문자열을 나타내는 리터럴입니다. [유효한 인증 방법](#authentication) 및 [네트워크 끝점](#specify-the-network-endpoint)을 지정 하는 방법을 참조 하세요.

* *SqlQuery* : `string` SQL 끝점에 대해 실행할 쿼리를 나타내는 리터럴입니다. 는 하나 이상의 행 집합을 반환 해야 하지만, 첫 번째 행 집합은 Kusto 쿼리의 나머지 부분에서 사용할 수 있습니다.

* *Sqlparameters* : `dynamic` 쿼리와 함께 매개 변수로 전달할 키-값 쌍을 보유 하는 형식의 상수 값입니다. (선택 사항)
  
* *Options* : `dynamic` 고급 설정을 키-값 쌍으로 포함 하는 형식의 상수 값입니다. 현재는 `token` 인증을 위해 SQL 끝점에 전달 되는 호출자 제공 AZURE AD 액세스 토큰을 전달 하기 위해만 설정할 수 있습니다. (선택 사항)

## <a name="examples"></a>예제

다음 예에서는 SQL 쿼리를 Azure SQL DB 데이터베이스로 보냅니다. 에서 모든 레코드를 검색 한 `[dbo].[Table]` 다음, Kusto 쪽에서 결과를 처리 합니다. 인증은 호출 하는 사용자의 Azure AD 토큰을 다시 사용 합니다. 

> [!NOTE]
> 이 예제는 이러한 방식으로 데이터를 필터링 하거나 프로젝션 하는 권장 사항으로 사용 하면 안 됩니다. Kusto 최적화 프로그램은 현재 Kusto와 SQL 간의 쿼리를 최적화 하려고 시도 하지 않으므로 가능한 가장 작은 데이터 집합을 반환 하도록 SQL 쿼리를 생성 해야 합니다.

```kusto
evaluate sql_request(
  'Server=tcp:contoso.database.windows.net,1433;'
    'Authentication="Active Directory Integrated";'
    'Initial Catalog=Fabrikam;',
  'select * from [dbo].[Table]')
| where Id > 0
| project Name
```

다음 예제는 사용자 이름/암호를 통해 SQL 인증을 수행 한다는 점을 제외 하 고는 이전 예제와 동일 합니다. 기밀성을 위해 여기에 난독 처리 되는 문자열을 사용 합니다.

```kusto
evaluate sql_request(
  'Server=tcp:contoso.database.windows.net,1433;'
    'Initial Catalog=Fabrikam;'
    h'User ID=USERNAME;'
    h'Password=PASSWORD;',
  'select * from [dbo].[Table]')
| where Id > 0
| project Name
```

다음 예에서는에서 모든 레코드를 검색 하 `[dbo].[Table]` 는 동안 다른 `datetime` 열을 추가한 다음 Kusto 쪽에서 결과를 처리 하는 AZURE sql DB 데이터베이스에 sql 쿼리를 보냅니다.
SQL 쿼리에 사용할 SQL 매개 변수 ()를 지정 합니다 `@param0` .

```kusto
evaluate sql_request(
  'Server=tcp:contoso.database.windows.net,1433;'
    'Authentication="Active Directory Integrated";'
    'Initial Catalog=Fabrikam;',
  'select *, @param0 as dt from [dbo].[Table]',
  dynamic({'param0': datetime(2020-01-01 16:47:26.7423305)}))
| where Id > 0
| project Name
```

## <a name="authentication"></a>인증

Sql_request 플러그 인은 SQL Server 끝점에 대 한 세 가지 인증 방법을 지원 합니다.

### <a name="azure-ad-integrated-authentication"></a>Azure AD 통합 인증 

`Authentication="Active Directory Integrated"`

  Azure AD 통합 인증은 선호 되는 방법입니다. 이 메서드는 사용자 또는 응용 프로그램이 Azure AD를 통해 Kusto에 인증 합니다. 그런 다음 동일한 토큰을 사용 하 여 SQL Server 네트워크 끝점에 액세스 합니다.

### <a name="usernamepassword-authentication"></a>사용자 이름/암호 인증

`User ID=...; Password=...;`

  Azure AD 통합 인증을 수행할 수 없는 경우 사용자 이름 및 암호 인증 지원이 제공 됩니다. 암호 정보가 Kusto를 통해 전송 되므로 가능 하면이 메서드를 사용 하지 마십시오.

### <a name="azure-ad-access-token"></a>Azure AD 액세스 토큰

`dynamic({'token': h"eyJ0..."})`

   Azure AD 액세스 토큰 인증 방법을 사용 하 여 호출자는 Kusto에서 SQL 끝점으로 전달 되는 액세스 토큰을 생성 합니다. 연결 문자열은, 또는와 같은 인증 정보를 포함 하지 않아야 합니다 `Authentication` `User ID` `Password` . 대신 `token` `Options` sql_request 플러그 인의 인수에서 액세스 토큰이 속성으로 전달 됩니다.
     
> [!WARNING]
> 기밀 정보 또는 보호 해야 하는 정보를 포함 하는 연결 문자열 및 쿼리는 모든 Kusto 추적에서 생략 되도록 난독 처리 되어야 합니다.
> 자세한 정보 롤업 난독 처리 되는 [문자열 리터럴](scalar-data-types/string.md#obfuscated-string-literals)을 참조 하세요.

## <a name="encryption-and-server-validation"></a>암호화 및 서버 유효성 검사

다음 연결 속성은 보안상의 이유로 SQL Server 네트워크 끝점에 연결 하는 경우 강제 적용 됩니다.

* `Encrypt` 는 무조건로 설정 됩니다 `true` .
* `TrustServerCertificate` 는 무조건로 설정 됩니다 `false` .

따라서 유효한 SSL/TLS 서버 인증서를 사용 하 여 SQL Server를 구성 해야 합니다.

## <a name="specify-the-network-endpoint"></a>네트워크 끝점 지정

연결 문자열의 일부로 SQL 네트워크 끝점을 지정 하는 것은 필수입니다.
적합한 구문은 다음과 같습니다.

`Server``=` `tcp:` *FQDN* [ `,` *포트* ]

위치:

* *FQDN* 은 끝점의 정규화 된 도메인 이름입니다.
* *포트* 는 끝점의 TCP 포트입니다. 기본적으로 `1433` 는로 가정 합니다.

> [!NOTE]
> 네트워크 끝점을 지정 하는 다른 형식은 지원 되지 않습니다.
> 예를 들어 `tcp:` SQL 클라이언트 라이브러리를 프로그래밍 방식으로 사용 하는 경우에도 접두사를 생략할 수 없습니다.

::: zone-end

::: zone pivot="azuremonitor"

이 기능은에서 지원 되지 않습니다 Azure Monitor

::: zone-end
