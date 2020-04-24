---
title: 표 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 테이블에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/30/2019
ms.openlocfilehash: fd47a8f59c716148dfc5f89ac1d4c7aca45a8e9c
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81509285"
---
# <a name="tables"></a>테이블

테이블은 데이터를 유지하는 명명된 엔터티입니다. 테이블에는 정렬된 [열](./columns.md)집합과 0개 이상의 데이터 행이 있으며 각 행에는 테이블의 각 열에 대해 하나의 데이터 값이 있습니다. 테이블의 행 순서는 알 수 없으며 본질적으로 결정되지 않은 일부 테이블 형식 연산자(예: [맨 위 연산자)를](../topoperator.md)제외하고는 일반적으로 쿼리에 영향을 미치지 않습니다.

테이블은 [저장된 함수와](./stored-functions.md)동일한 네임스페이스를 차지하므로 저장된 함수와 테이블 모두 같은 이름을 가지면 저장된 함수가 선택됩니다.

**참고 사항**  

* 테이블 이름은 대/소문자를 구분합니다.
* 테이블 이름은 엔터티 [이름에](./entity-names.md)대한 규칙을 따릅니다.
* 데이터베이스당 최대 테이블 한도는 10,000개입니다.


테이블 을 만들고 관리하는 방법에 대한 자세한 내용은 [테이블 관리에서](../../management/tables.md) 찾을 수 있습니다.

## <a name="table-references"></a>테이블 참조

테이블을 참조하는 가장 간단한 방법은 해당 이름을 사용하는 것입니다. 컨텍스트에서 데이터베이스에 있는 모든 테이블에 대해 이 작업을 수행할 수 있습니다. 예를 들어 다음 쿼리는 현재 데이터베이스 테이블의 `StormEvents` 레코드를 계산합니다.

```kusto
StormEvents
| count
```

위의 쿼리를 작성하는 것과 동일한 방법은 테이블 이름을 이스케이프하는 것입니다.

```kusto
["StormEvents"]
| count
```

테이블은 데이터베이스(또는 데이터베이스 및 클러스터)를 명시적으로 기록하여 여러 데이터베이스와 클러스터의 데이터를 결합하는 쿼리를 작성할 수 있도록 합니다. 예를 들어, 다음 쿼리는 호출자의 대상 데이터베이스에 액세스할 수 있는 한 컨텍스트의 모든 데이터베이스에서 작동합니다.

```kusto
cluster("https://help.kusto.windows.net").database("Samples").StormEvents
| count
```

해당 함수에 대한 인수가 상수로 평가되는 한 [table() 특수 함수를](../tablefunction.md)사용하여 테이블을 참조할 수도 있습니다. 다음은 그 예입니다.

```kusto
let counter=(TableName:string) { table(TableName) | count };
counter("StormEvents")
```