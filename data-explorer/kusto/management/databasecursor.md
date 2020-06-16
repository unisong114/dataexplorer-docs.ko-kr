---
title: 데이터베이스 커서-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 데이터베이스 커서에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 75dc0aa0ff23bfb4f08be9fac84fa34cf9526508
ms.sourcegitcommit: 8e097319ea989661e1958efaa1586459d2b69292
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/15/2020
ms.locfileid: "84780629"
---
# <a name="database-cursors"></a>데이터베이스 커서

데이터베이스 **커서** 는 데이터베이스를 여러 번 쿼리할 수 있도록 하는 데이터베이스 수준 개체입니다. 쿼리와 병렬로 발생 하는 작업이 있는 경우에도 일관 된 결과를 얻을 수 있습니다 `data-append` `data-retention` .

데이터베이스 커서는 다음과 같은 두 가지 중요 한 시나리오를 해결 하도록 설계 되었습니다.

* 동일한 쿼리를 여러 번 반복 하 고 쿼리가 "동일한 데이터 집합"을 나타내는 경우 동일한 결과를 얻을 수 있습니다.

* "정확히 한 번" 쿼리를 만들 수 있습니다. 이 쿼리는 데이터를 사용할 수 없기 때문에 이전 쿼리에 표시 되지 않는 데이터를 "표시" 합니다.
   예를 들어이 쿼리를 사용 하면 같은 레코드를 두 번 처리 하거나 레코드를 실수로 건너뛰는 것을 걱정 하지 않고 테이블에 새로 도착 한 모든 데이터를 반복할 수 있습니다.

데이터베이스 커서는 쿼리 언어에서 형식의 스칼라 값으로 표현 됩니다 `string` . 실제 값은 불투명 한 것으로 간주 되어야 하며, 값을 저장 하거나 아래에 설명 된 커서 함수를 사용 하는 것 외의 다른 작업은 지원 되지 않습니다.

## <a name="cursor-functions"></a>커서 함수

Kusto는 위의 두 가지 시나리오를 구현 하는 데 도움이 되는 세 가지 함수를 제공 합니다.

* [cursor_current ()](../query/cursorcurrent.md):이 함수를 사용 하 여 데이터베이스 커서의 현재 값을 검색 합니다.
   이 값은 다른 두 함수에 대 한 인수로 사용할 수 있습니다.
   이 함수에는 동의어도 있습니다 `current_cursor()` .

* [cursor_after (rhs: string)](../query/cursorafterfunction.md): [IngestionTime 정책이](ingestiontime-policy.md) 설정 된 테이블 레코드에서이 특수 함수를 사용할 수 있습니다. 이 메서드는 `bool` 레코드의 `ingestion_time()` 데이터베이스 커서 값이 데이터베이스 커서 값 뒤에 있는지 여부를 나타내는 형식의 스칼라 값을 반환 `rhs` 합니다.

* [cursor_before_or_at (rhs: string)](../query/cursorbeforeoratfunction.md): [IngestionTime 정책이](ingestiontime-policy.md) 설정 된 테이블 레코드에서이 특수 함수를 사용할 수 있습니다. 이 메서드는 `bool` 레코드의 `ingestion_time()` 데이터베이스 커서 값이 데이터베이스 커서 값 뒤에 있는지 여부를 나타내는 형식의 스칼라 값을 반환 `rhs` 합니다.

두 가지 특수 함수 ( `cursor_after` 및 `cursor_before_or_at` )에도 부작용이 있습니다. 사용 되는 경우 Kusto는 **데이터베이스 커서의 현재 값** 을 `@ExtendedProperties` 쿼리의 결과 집합으로 내보냅니다. 커서의 속성 이름은이 `Cursor` 고 해당 값은 단일 `string` 입니다. 

다음은 그 예입니다.

```json
{"Cursor" : "636040929866477946"}
```

## <a name="restrictions"></a>제한

[IngestionTime 정책이](ingestiontime-policy.md) 사용 하도록 설정 된 테이블에만 데이터베이스 커서를 사용할 수 있습니다. 이러한 테이블의 각 레코드는 레코드가 수집 때 적용 된 데이터베이스 커서의 값과 연결 됩니다.
따라서 [ingestion_time ()](../query/ingestiontimefunction.md) 함수를 사용할 수 있습니다.

데이터베이스에 [IngestionTime 정책이](ingestiontime-policy.md) 정의 된 테이블이 하나 이상 있는 경우를 제외 하 고 데이터베이스 커서 개체는 의미 있는 값을 포함 하지 않습니다.
이러한 테이블을 참조 하는 이러한 테이블과 쿼리를 실행 하는 쿼리를 실행 하는 데 필요에 따라이 값은 업데이트 하도록 보장 됩니다. 다른 경우에 업데이트 될 수도 있고 업데이트 되지 않을 수도 있습니다.

수집 프로세스는 먼저 데이터를 커밋한 다음 쿼리를 사용할 수 있도록 하 고 각 레코드에 실제 커서 값을 할당 합니다. 데이터베이스 커서를 사용 하 여 수집 완료 후에 즉시 데이터를 쿼리해야 하는 경우 아직 커서 값을 할당 하지 않았기 때문에 추가 된 마지막 레코드를 결과에 통합 하지 못할 수 있습니다. 또한 현재 데이터베이스 커서 값을 반복적으로 검색 하면 커서 커밋이 값을 업데이트할 수 있기 때문에에서 수집을 수행한 경우에도 동일한 값이 반환 될 수 있습니다.

## <a name="example-processing-records-exactly-once"></a>예: 레코드를 정확히 한 번 처리

`Employees`스키마가 있는 테이블의 경우 `[Name, Salary]` 테이블에 수집 새 레코드를 지속적으로 처리 하려면 다음 프로세스를 사용 합니다.

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
