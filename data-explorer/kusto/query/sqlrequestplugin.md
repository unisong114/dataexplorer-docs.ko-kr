---
title: sql_request 플러그 인-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기에서 sql_request 플러그 인을 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 725021ad8089d7e9ad4f897bd5a1c68f6912bf7a
ms.sourcegitcommit: d885c0204212dd83ec73f45fad6184f580af6b7e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82737286"
---
# <a name="sql_request-plugin"></a>sql_request 플러그 인

::: zone pivot="azuredataexplorer"

  `evaluate``sql_request` `,` *SqlQuery* `,` *Options* *ConnectionString* `,` ConnectionString SqlQuery [sqlparameters [Options]] *SqlParameters* `(``)`

플러그 `sql_request` 인은 SQL Server 네트워크 끝점으로 SQL 쿼리를 보내고 결과에서 첫 번째 행 집합을 반환 합니다.

**인수**

* *ConnectionString*: SQL Server `string` 네트워크 끝점을 가리키는 연결 문자열을 나타내는 리터럴입니다. 유효한 인증 방법 및 네트워크 끝점을 지정 하는 방법에 대 한 자세한 내용은 아래를 참조 하세요.

* *SqlQuery*: SQL `string` 끝점에 대해 실행할 쿼리를 나타내는 리터럴입니다. 는 하나 이상의 행 집합을 반환 해야 하지만, 첫 번째 행 집합은 Kusto 쿼리의 나머지 부분에서 사용할 수 있습니다.

* *Sqlparameters*: 쿼리와 함께 매개 변수로 전달할 `dynamic` 키-값 쌍을 보유 하는 형식의 상수 값입니다. 선택 사항입니다.
  
* *Options*: 고급 설정을 키-값 `dynamic` 쌍으로 포함 하는 형식의 상수 값입니다. 현재는 `token` 인증을 위해 SQL 끝점으로 전달 되는 호출자 제공 AAD 액세스 토큰을 전달 하기 위해만 설정할 수 있습니다. 선택 사항입니다.

**예**

다음 예에서는에서 `[dbo].[Table]`모든 레코드를 검색 하는 AZURE sql DB 데이터베이스로 sql 쿼리를 보낸 다음 Kusto 쪽에서 결과를 처리 합니다. 인증은 호출 하는 사용자의 AAD 토큰을 다시 사용 합니다.

참고:이 예제는 이러한 방식으로 데이터를 필터링/프로젝트 하기 위한 권장 사항으로 사용 하면 안 됩니다. 현재 Kusto 최적화 프로그램은 Kusto와 SQL 간에 쿼리를 최적화 하려고 하지 않으므로 가능한 가장 작은 데이터 집합을 반환 하기 위해 SQL 쿼리를 생성 하는 것이 좋습니다.

```kusto
evaluate sql_request(
  'Server=tcp:contoso.database.windows.net,1433;'
    'Authentication="Active Directory Integrated";'
    'Initial Catalog=Fabrikam;',
  'select * from [dbo].[Table]')
| where Id > 0
| project Name
```

다음 예제는 사용자 이름/암호를 통해 SQL 인증을 수행 한다는 점을 제외 하 고는 이전 예제와 동일 합니다. 기밀성을 위해 여기에 난독 처리 된 문자열을 사용 합니다.

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

Sql_request 플러그 인은 SQL Server 끝점에 대 한 세 가지 인증 방법을 지원 합니다.

* **Aad 통합 인증** (`Authentication="Active Directory Integrated"`): 사용자 또는 응용 프로그램이 aad를 kusto를 통해 인증 하 고, 동일한 토큰을 사용 하 여 SQL Server 네트워크 끝점에 액세스 하는 기본 설정 방법입니다.

* **사용자 이름/암호 인증** (`User ID=...; Password=...;`): AAD 통합 인증을 수행할 수 없는 경우이 메서드에 대 한 지원이 제공 됩니다. 암호 정보가 Kusto를 통해 전송 되므로 가능 하면이 메서드를 사용 하지 마십시오.

* **AAD 액세스 토큰** (`dynamic({'token': h"eyJ0..."})`):이 인증 방법을 사용 하면 액세스 토큰이 호출자에 의해 생성 되 고 kusto에서 SQL 끝점으로 전달 됩니다. 연결 문자열에, `Authentication` `User ID`또는 `Password`와 같은 인증 정보가 포함 되어서는 안 됩니다. 대신 sql_request 플러그 인의 `token` `Options` 인수에서 액세스 토큰이 속성으로 전달 됩니다.
     
> [!WARNING]
> 기밀 정보나 보호 해야 하는 정보를 포함 하는 연결 문자열 및 쿼리는 모든 Kusto 추적에서 생략 되도록 난독 처리 되어야 합니다.
> 자세한 내용은 난독 처리 되는 [문자열 리터럴](scalar-data-types/string.md#obfuscated-string-literals) 을 참조 하세요.

**암호화 및 서버 유효성 검사**

다음 연결 속성은 보안상의 이유로 SQL Server 네트워크 끝점에 연결 하는 경우 강제 적용 됩니다.

* `Encrypt`는 무조건로 `true` 설정 됩니다.
* `TrustServerCertificate`는 무조건로 `false` 설정 됩니다.

따라서 유효한 SSL/TLS 서버 인증서를 사용 하 여 SQL Server를 구성 해야 합니다.

**네트워크 끝점 지정**

연결 문자열의 일부로 SQL 네트워크 끝점을 지정 하는 것은 필수입니다.
적합한 구문은 다음과 같습니다.

`Server``=` `,` *Port*FQDN [포트] *FQDN* `tcp:`

위치:

* *FQDN* 은 끝점의 정규화 된 도메인 이름입니다.

* *포트* 는 끝점의 TCP 포트입니다. 기본적으로 `1433` 는로 가정 합니다.

> [!NOTE]
> 네트워크 끝점을 지정 하는 다른 형식은 지원 되지 않습니다.
> 예를 들어 SQL 클라이언트 라이브러리를 프로그래밍 방식 `tcp:` 으로 사용 하는 경우에도 접두사를 생략할 수 없습니다.



::: zone-end

::: zone pivot="azuremonitor"

이 기능은에서 지원 되지 않습니다 Azure Monitor

::: zone-end
