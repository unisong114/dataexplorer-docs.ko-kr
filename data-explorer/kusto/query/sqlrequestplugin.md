---
title: sql_request 플러그인 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 sql_request 플러그인에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: f0c0837c6bb8e4dcd3cf2e28af18d02c19edb676
ms.sourcegitcommit: 01eb9aaf1df2ebd5002eb7ea7367a9ef85dc4f5d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81766167"
---
# <a name="sql_request-plugin"></a>sql_request 플러그 인

::: zone pivot="azuredataexplorer"

  `evaluate``sql_request` `,` *Options* *ConnectionString* `,` *SqlQuery* `,` *SqlParameters* 연결 문자열 SqlQuery [ SqlParameters [ 옵션 ]] `(``)`

플러그인은 `sql_request` SQL Server 네트워크 끝점으로 SQL 쿼리를 보내고 결과의 첫 번째 행 집합을 반환합니다.

**인수**

* *연결 문자열* `string` : SQL Server 네트워크 끝점을 가리키는 연결 문자열을 나타내는 리터럴입니다. 유효한 인증 방법과 네트워크 끝점을 지정하는 방법은 아래를 참조하십시오.

* *SqlQuery*: `string` SQL 끝점에 대해 실행될 쿼리를 나타내는 리터럴입니다. 하나 이상의 행 집합을 반환해야 하지만 Kusto 쿼리의 나머지 부분에서는 첫 번째 행 집합만 사용할 수 있습니다.

* *SqlParameters*: 쿼리와 `dynamic` 함께 매개 변수로 전달할 키-값 쌍을 보유하는 형식의 상수 값입니다. (선택 사항)
  
* *옵션*: 키-값 `dynamic` 쌍으로 고급 설정을 보유하는 형식의 상수 값입니다. 인증을 `token` 위해 SQL 끝점으로 전달되는 발신자가 제공한 AAD 액세스 토큰을 전달하려면 현재만 설정할 수 있습니다. (선택 사항)

**예**

다음 예제에서는 에서 모든 레코드를 검색 하는 Azure `[dbo].[Table]`SQL DB 데이터베이스에 SQL 쿼리를 보낸 다음 Kusto 측에서 결과 처리 합니다. 인증은 호출하는 사용자의 AAD 토큰을 다시 사용합니다.

참고: 이 예제는 이러한 방식으로 데이터를 필터링/프로젝트하는 권장 사항으로 간주해서는 안 됩니다. 현재 Kusto 최적화 프로그램은 Kusto와 SQL 간의 쿼리를 최적화하려고 시도하지 않으므로 가능한 가장 작은 데이터 집합을 반환하기 위해 SQL 쿼리를 구성하는 것이 좋습니다.

```kusto
evaluate sql_request(
  'Server=tcp:contoso.database.windows.net,1433;'
    'Authentication="Active Directory Integrated";'
    'Initial Catalog=Fabrikam;',
  'select * from [dbo].[Table]')
| where Id > 0
| project Name
```

다음 예제는 SQL 인증이 사용자 이름/암호에 의해 수행된다는 점을 제외하면 이전 예제와 동일합니다. 기밀성을 위해 난독 처리된 문자열을 여기에 사용합니다.

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

**인증**

sql_request 플러그인은 SQL Server 끝점에 대한 세 가지 인증 방법을 지원합니다.

* **AAD 통합** `Authentication="Active Directory Integrated"`인증 (): 사용자 또는 응용 프로그램이 AAD를 통해 Kusto로 인증하고 동일한 토큰을 사용하여 SQL Server 네트워크 끝점에 액세스하는 데 사용되는 기본 방법입니다.

* **사용자 이름/암호** `User ID=...; Password=...;`인증 (): AAD 통합 인증을 수행할 수 없는 경우 이 메서드에 대한 지원이 제공됩니다. 비밀 정보가 Kusto를 통해 전송되기 때문에 가능하면이 방법을 피하십시오.

* **AAD 액세스** `dynamic({'token': h"eyJ0..."})`토큰 ( ): 이 인증 방법을 사용하면 액세스 토큰이 호출자에 의해 생성되고 Kusto가 SQL 끝점으로 전달합니다. 연결 문자열에는 에 `Authentication` `User ID`대한 인증 정보가 `Password`포함되어서는 안 됩니다. 대신 액세스 토큰은 sql_request `token` 플러그인의 `Options` 인수에서 속성으로 전달됩니다.
     
> [!WARNING]
> 기밀 정보 또는 보호해야 하는 정보가 포함된 연결 문자열 및 쿼리는 Kusto 추적에서 생략되도록 난독 처리해야 합니다.
> 자세한 내용은 [난독 처리된 문자열 리터럴을](scalar-data-types/string.md#obfuscated-string-literals) 참조하십시오.

**암호화 및 서버 유효성 검사**

보안상의 이유로 SQL Server 네트워크 끝점에 연결할 때 다음 연결 속성이 강제로 수행됩니다.

* `Encrypt`무조건 `true` 설정됩니다.
* `TrustServerCertificate`무조건 `false` 설정됩니다.

따라서 SQL Server는 유효한 SSL/TLS 서버 인증서로 구성해야 합니다.

**네트워크 끝점 지정**

SQL 네트워크 끝점을 연결 문자열의 일부로 지정하는 것은 필수입니다.
적합한 구문은 다음과 같습니다.

`Server``=` *FQDN* `,` *Port*FQDN [ 포트 ] `tcp:`

위치:

* *FQDN은* 끝점의 정규화된 도메인 이름입니다.

* *포트는* 끝점의 TCP 포트입니다. 기본적으로 가정됩니다. `1433`

> [!NOTE]
> 네트워크 끝점을 지정하는 다른 형식은 지원되지 않습니다.
> 예를 들어 프로그래밍 방식으로 SQL 클라이언트 `tcp:` 라이브러리를 사용할 때 접두사를 생략할 수 없습니다.



::: zone-end

::: zone pivot="azuremonitor"

Azure 모니터에서는 이 성능이 지원되지 않습니다.

::: zone-end
