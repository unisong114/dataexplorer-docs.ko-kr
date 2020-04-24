---
title: 데이터베이스 커서 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 데이터베이스 커서에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 90ec677a7eaf1f326509828b5415b022742fd9ed
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81521321"
---
# <a name="database-cursors"></a>데이터베이스 커서

**데이터베이스 커서는** 데이터베이스 수준 개체로, 쿼리와 병행하여 진행 중인 데이터 부화/데이터 보존 작업이 진행 중인 경우에도 데이터베이스를 여러 번 쿼리하고 일관된 결과를 얻을 수 있습니다.

데이터베이스 커서는 다음 두 가지 중요한 시나리오를 처리하도록 설계되었습니다.

* 쿼리가 "동일한 데이터 집합"을 나타내는 한 동일한 쿼리를 여러 번 반복하고 동일한 결과를 얻을 수 있는 기능입니다.

* "정확히 한 번" 쿼리를 수행하는 기능(이전 쿼리에서 볼 수 없었던 데이터만 "보는" 쿼리는 데이터를 사용할 수 없었기 때문입니다).
   예를 들어, 동일한 레코드를 두 번 처리하거나 실수로 레코드를 건너뛸 까봐 두려워하지 않고 테이블에 새로 도착한 모든 데이터를 반복할 수 있습니다.

데이터베이스 커서는 쿼리 언어로 형식의 `string`스칼라 값으로 표시됩니다. 실제 값은 불투명한 것으로 간주되어야 하며 값을 저장하거나 아래에 언급된 커서 함수를 사용하는 것 외에는 지원이 없습니다.

## <a name="cursor-functions"></a>커서 함수

Kusto는 위의 두 시나리오를 구현하는 데 도움이 되는 세 가지 함수를 제공합니다.

* [cursor_current()](../query/cursorcurrent.md): 이 함수를 사용하여 데이터베이스 커서의 현재 값을 검색합니다.
   이 값을 다른 두 함수에 대한 인수로 사용할 수 있습니다.
   이 함수에는 동의어도 `current_cursor()`있습니다.

* [cursor_after(rhs:string)](../query/cursorafterfunction.md): 이 특수 함수는 [IngestionTime 정책을](ingestiontime-policy.md) 사용하도록 설정한 테이블 레코드에서 사용할 수 있습니다. 레코드의 데이터베이스 커서 값이 `bool` `rhs` 데이터베이스 커서 값 `ingestion_time()` 다음으로 오는지 여부를 나타내는 형식의 스칼라 값을 반환합니다.

* [cursor_before_or_at(rhs:string)](../query/cursorbeforeoratfunction.md): 이 특수 함수는 [IngestionTime 정책을](ingestiontime-policy.md) 사용하도록 설정한 테이블 레코드에서 사용할 수 있습니다. 레코드의 데이터베이스 커서 값이 `bool` `rhs` 데이터베이스 커서 값 `ingestion_time()` 다음으로 오는지 여부를 나타내는 형식의 스칼라 값을 반환합니다.

두 개의 특수`cursor_after` 함수 `cursor_before_or_at`(및)에도 부작용이 있습니다: 사용할 때 Kusto는 **데이터베이스 커서의 현재** 값을 `@ExtendedProperties` 쿼리의 결과 집합에 내보냅니다. 커서의 속성 이름은 `Cursor`은 및 해당 값은 `string`single입니다. 다음은 그 예입니다.

```json
{"Cursor" : "636040929866477946"}
```

## <a name="restrictions"></a>제한

데이터베이스 커서는 [IngestionTime 정책이](ingestiontime-policy.md) 활성화된 테이블에서만 사용할 수 있습니다. 이러한 테이블의 각 레코드는 레코드를 가져올 때 적용되었던 데이터베이스 커서값과 연결되어 있으므로 [ingestion_time()](../query/ingestiontimefunction.md) 함수를 사용할 수 있습니다.

데이터베이스에 [IngestionTime 정책이](ingestiontime-policy.md) 정의된 테이블이 하나 이상 있는 경우 데이터베이스 커서 개체에는 의미 있는 값이 없습니다.
또한 이 값은 이러한 테이블에 대한 인기 기록에서 필요에 따라 업데이트되고 이러한 테이블을 참조하는 쿼리가 실행되도록 보장됩니다. 다른 경우에는 업데이트될 수도 있으며 업데이트되지 않을 수도 있습니다.

수집 프로세스는 먼저 데이터를 커밋하여 쿼리할 수 있도록 한 다음 각 레코드에 실제 커서 값을 할당합니다. 즉, 데이터베이스 커서를 사용하여 수집 완료 직후데이터를 쿼리하려고 하면 커서 값이 아직 할당되지 않았기 때문에 결과가 추가된 마지막 레코드를 아직 통합하지 못할 수 있습니다. 마찬가지로 현재 데이터베이스 커서 값을 반복적으로 검색하면 커서 커밋만 해당 값이 업데이트되므로(그 사이에 인베이션이 수행된 경우에도) 동일한 값을 반환할 수 있습니다.

## <a name="example-processing-of-records-exactly-once"></a>예: 레코드 처리 정확히 한 번

스키마가 `Employees` `[Name, Salary]`있는 테이블을 가정합니다.
새 레코드가 테이블에 들어오면서 새 레코드를 계속 처리하려면 다음 절차를 사용합니다.

```kusto
// [Once] Enable the IngestionTime policy on table Employees
.set table Employees policy ingestiontime true

// [Once] Get all the data that the Employees table currently holds 
Employees | where cursor_after('')

// The query above will return the database cursor value in
// the @ExtendedProperties result set. Lets assume that it returns
// the value '636040929866477946'

// [Many] Get all the data that was added to the Employees table
// since the previous query was run using the previously-returned
// database cursor 
Employees | where cursor_after('636040929866477946') // -> 636040929866477950

Employees | where cursor_after('636040929866477950') // -> 636040929866479999

Employees | where cursor_after('636040929866479999') // -> 636040939866479000
```