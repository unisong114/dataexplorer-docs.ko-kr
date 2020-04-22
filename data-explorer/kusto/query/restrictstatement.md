---
title: 제한 문 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기에서 제한 문에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: cbd21c01956f817c5db19a93104028dba2b2b2b4
ms.sourcegitcommit: 01eb9aaf1df2ebd5002eb7ea7367a9ef85dc4f5d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81765978"
---
# <a name="restrict-statement"></a>Restrict 문

::: zone pivot="azuredataexplorer"

제한 문은 다음에 있는 쿼리 문에 표시되는 테이블/뷰 엔터티 집합을 제한합니다. 예를 들어 두 개의 테이블(,`A` `B`)을 포함하는 데이터베이스에서 응용 프로그램은 나머지 `B` 쿼리가 액세스하지 못하도록 하고 `A` 뷰를 사용하여 제한된 형식의 테이블만 "볼" 수 있습니다.

제한 문의 주요 시나리오는 사용자의 쿼리를 허용 하 고 이러한 쿼리를 통해 행 수준 보안 메커니즘을 적용 하려는 중간 계층 응용 프로그램에 대 한 것입니다. 중간 계층 응용 프로그램은 사용자의 데이터에 대한 액세스를 제한하는 보기를 정의하는 let 문 집합인 **논리 모델(예:** `T | where UserId == "..."`)으로 사용자의 쿼리를 접두사할 수 있습니다. 마지막 문이 추가되면 논리 모델에 대한 사용자의 액세스만 제한됩니다.

**구문**

`restrict``access` `,` [*엔티티스스펙티어 [...]]* `to` `(``)`

*EntitySpecifier가* 다음 중 하나인 경우:
* let 문에 의해 테이블 형식 보기로 정의된 식별자입니다.
* 공용 구조자 문에서 사용하는 것과 유사한 테이블 참조입니다.
* 패턴 선언에 의해 정의된 패턴입니다.

제한 문에 의해 지정되지 않은 모든 테이블, 테이블 형식 보기 또는 패턴은 쿼리의 나머지 부분에 "보이지 않음"이 됩니다. 

**참고 사항**

제한 문을 사용하여 다른 데이터베이스 또는 클러스터의 엔터티에 대한 액세스를 제한할 수 있습니다(와일드카드는 클러스터 이름에서 지원되지 않음).

**인수**

limit 문은 엔터티의 이름 확인 중에 허용 제한을 정의하는 하나 이상의 매개 변수를 얻을 수 있습니다. 엔터티는 다음과 같은 것일 수 있습니다.
- 문 앞에 `restrict` 문장이 [나타나도록 하십시오.](./letstatement.md) 

```kusto
// Limit access to 'Test' let statement only
let Test = () { print x=1 };
restrict access to (Test);
```

- 데이터베이스 메타데이터에 정의된 [테이블](../management/tables.md) 또는 [함수입니다.](../management/functions.md)

```kusto
// Assuming the database that the query uses has table Table1 and Func1 defined in the metadata, 
// and other database 'DB2' has Table2 defined in the metadata
 
restrict access to (database().Table1, database().Func1, database('DB2').Table2);
```

- [let 문](./letstatement.md) 또는 테이블/함수의 배수와 일치시킬 수 있는 와일드카드 패턴  

```kusto
let Test1 = () { print x=1 };
let Test2 = () { print y=1 };
restrict access to (*);
// Now access is restricted to Test1, Test2 and no tables/functions are accessible.

// Assuming the database that the query uses has table Table1 and Func1 defined in the metadata.
// Assuming that database 'DB2' has table Table2 and Func2 defined in the metadata
restricts access to (database().*);
// Now access is restricted to all tables/functions of the current database ('DB2' is not accessible).

// Assuming the database that the query uses has table Table1 and Func1 defined in the metadata.
// Assuming that database 'DB2' has table Table2 and Func2 defined in the metadata
restricts access to (database('DB2').*);
// Now access is restricted to all tables/functions of the database 'DB2'
```


**예**

다음 예제에서는 중간 계층 응용 프로그램이 사용자가 다른 사용자의 데이터를 쿼리하지 못하도록 하는 논리 모델로 사용자의 쿼리를 준비하는 방법을 보여 주습니다.

```kusto
// Assume the database has a single table, UserData,
// with a column called UserID and other columns that hold
// per-user private information.
//
// The middle-tier application generates the following statements.
// Note that "username@domain.com" is something the middle-tier application
// derives per-user as it authenticates the user.
let RestrictedData = view () { Data | where UserID == "username@domain.com" };
restrict access to (RestrictedData);
// The rest of the query is something that the user types.
// This part can only reference RestrictedData; attempting to reference Data
// will fail.
RestrictedData | summarize IrsLovesMe=sum(Salary) by Year, Month
```

```kusto
// Restricting access to Table1 in the current database (database() called without parameters)
restrict access to (database().Table1);
Table1 | count

// Restricting acess to Table1 in the current database and Table2 in database 'DB2'
restrict access to (database().Table1, database('DB2').Table2);
union 
    (Table1),
    (database('DB2').Table2))
| count

// Restricting access to Test statement only
let Test = () { range x from 1 to 10 step 1 };
restrict access to (Test);
Test
 
// Assume that there is a table called Table1, Table2 in the database
let View1 = view () { Table1 | project Column1 };
let View2 = view () { Table2 | project Column1, Column2 };
restrict access to (View1, View2);
 
// When those statements appear before the command - the next works
let View1 = view () { Table1 | project Column1 };
let View2 = view () { Table2 | project Column1, Column2 };
restrict access to (View1, View2);
View1 |  count
 
// When those statements appear before the command - the next access is not allowed
let View1 = view () { Table1 | project Column1 };
let View2 = view () { Table2 | project Column1, Column2 };
restrict access to (View1, View2);
Table1 |  count
```

::: zone-end

::: zone pivot="azuremonitor"

Azure 모니터에서는 이 성능이 지원되지 않습니다.

::: zone-end
