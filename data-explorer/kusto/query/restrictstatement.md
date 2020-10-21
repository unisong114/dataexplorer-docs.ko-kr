---
title: Restrict 문-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 Restrict 문에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: f510e62e6b1ad828f0e132bbad214dc7119f8235
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92243028"
---
# <a name="restrict-statement"></a>Restrict 문

::: zone pivot="azuredataexplorer"

Restrict 문은 쿼리 문 뒤에 표시 되는 테이블/뷰 엔터티 집합을 제한 합니다. 예를 들어 두 개의 테이블이 포함 된 데이터베이스에서 ( `A` , `B` ) 응용 프로그램은 쿼리의 나머지 부분이 액세스 하는 것을 방지 하 `B` 고 `A` 뷰를 사용 하 여 제한 된 형태의 테이블을 "참조" 하는 것을 방지할 수 있습니다.

Restrict 문의 주요 시나리오는 사용자의 쿼리를 수락 하 고 해당 쿼리에 대해 행 수준 보안 메커니즘을 적용 하려는 중간 계층 응용 프로그램에 대 한 것입니다. 중간 계층 응용 프로그램은 사용자의 데이터에 대 한 액세스를 제한 하는 뷰를 정의 하는 let 문의 집합인 **논리 모델**을 사용 하 여 사용자의 쿼리에 접두사를 지정할 수 있습니다 (예: `T | where UserId == "..."` ). 마지막으로 추가 되는 문은 사용자의 논리적 모델만 액세스를 제한 합니다.

> [!NOTE]
> Restrict 문은 다른 데이터베이스 또는 클러스터의 엔터티에 대 한 액세스를 제한 하는 데 사용할 수 있습니다 (와일드 카드는 클러스터 이름에서 지원 되지 않음).

## <a name="syntax"></a>구문

`restrict``access` `to` `(` [*Entityspecifier* [ `,` ...]]`)`

여기서 *Entityspecifier* 는 다음 중 하나입니다.
* Let 문에 의해 테이블 형식 뷰로 정의 된 식별자입니다.
* Union 문에 사용 되는 것과 비슷한 테이블 참조입니다.
* 패턴 선언에 의해 정의 되는 패턴입니다.

Restrict 문으로 지정 되지 않은 모든 테이블, 테이블 형식 뷰 또는 패턴은 쿼리의 나머지 부분에 대해 "보이지 않는" 상태가 됩니다. 

## <a name="arguments"></a>인수

Restrict 문은 엔터티 이름을 확인 하는 동안 허용 제한을 정의 하는 하나 이상의 매개 변수를 가져올 수 있습니다. 엔터티는 다음과 같을 수 있습니다.
* 문 앞에 [let 문을](./letstatement.md) 표시 `restrict` 합니다. 

  ```kusto
  // Limit access to 'Test' let statement only
  let Test = () { print x=1 };
  restrict access to (Test);
  ```

* 데이터베이스 메타 데이터에 정의 된 [테이블](../management/tables.md) 또는 [함수](../management/functions.md)

    ```kusto
    // Assuming the database that the query uses has table Table1 and Func1 defined in the metadata, 
    // and other database 'DB2' has Table2 defined in the metadata
    
    restrict access to (database().Table1, database().Func1, database('DB2').Table2);
    ```

* [Let 문이나](./letstatement.md) 테이블/함수의 배수를 일치 시킬 수 있는 와일드 카드 패턴  

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

## <a name="examples"></a>예

다음 예제에서는 중간 계층 응용 프로그램에서 사용자가 다른 사용자의 데이터를 쿼리할 수 없도록 하는 논리 모델을 사용 하 여 사용자의 쿼리를 추가 하는 방법을 보여 줍니다.

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

// Restricting access to Table1 in the current database and Table2 in database 'DB2'
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

이 기능은에서 지원 되지 않습니다 Azure Monitor

::: zone-end
