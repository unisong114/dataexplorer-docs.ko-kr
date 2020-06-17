---
title: 테이블-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 테이블에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/30/2019
ms.openlocfilehash: fa97777da8173034098037f1aceec385a4c206de
ms.sourcegitcommit: 3848b8db4c3a16bda91c4a5b7b8b2e1088458a3a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/16/2020
ms.locfileid: "84818610"
---
# <a name="tables"></a>테이블

테이블은 데이터를 유지하는 명명된 엔터티입니다. 테이블에는 정렬 된 [열](./columns.md)집합과 0 개 이상의 데이터 행이 있습니다. 각 행에는 테이블의 각 열에 대해 하나의 데이터 값이 포함 됩니다. 테이블의 행 순서는 알 수 없으며, 기본적으로 결정 되지 않는 일부 테이블 형식 연산자 (예: [top 연산자](../topoperator.md))를 제외 하 고는 쿼리에 영향을 주지 않습니다.

테이블은 [저장 된 함수와](./stored-functions.md)동일한 네임 스페이스를 차지 합니다.
저장 된 함수와 테이블의 이름이 같으면 저장 된 함수가 선택 됩니다.

**참고 사항**  

* 테이블 이름은 대/소문자를 구분 합니다.
* 테이블 이름은 [엔터티 이름](./entity-names.md)에 대 한 규칙을 따릅니다.
* 데이터베이스당 최대 테이블 제한은 1만입니다.


테이블을 만들고 관리 하는 방법에 대 한 자세한 내용은 [테이블 관리](../../management/tables.md) 에서 찾을 수 있습니다.

## <a name="table-references"></a>테이블 참조

테이블을 참조 하는 가장 간단한 방법은 해당 이름을 사용 하는 것입니다. 이 참조는 컨텍스트에서 데이터베이스에 있는 모든 테이블에 대해 수행할 수 있습니다. 예를 들어 다음 쿼리는 현재 데이터베이스 테이블의 레코드 수를 계산 합니다 `StormEvents` .

```kusto
StormEvents
| count
```

위의 쿼리를 작성 하는 것과 동일한 방법은 테이블 이름을 이스케이프 처리 하는 것입니다.

```kusto
["StormEvents"]
| count
```

테이블이 있는 데이터베이스 (또는 데이터베이스 및 클러스터)에 명시적으로 주의 하 여 테이블을 참조할 수도 있습니다. 그런 다음 여러 데이터베이스 및 클러스터의 데이터를 결합 하는 쿼리를 작성할 수 있습니다. 예를 들어 다음 쿼리는 호출자에 게 대상 데이터베이스에 대 한 액세스 권한이 있는 경우 컨텍스트의 모든 데이터베이스에서 작동 합니다.

```kusto
cluster("https://help.kusto.windows.net").database("Samples").StormEvents
| count
```

이 함수에 대 한 인수가 상수로 계산 되는 [한 table () 특수 함수](../tablefunction.md)를 사용 하 여 테이블을 참조할 수도 있습니다. 예를 들어:

```kusto
let counter=(TableName:string) { table(TableName) | count };
counter("StormEvents")
```

> [!NOTE]
> `table()`특수 함수를 사용 하 여 테이블 데이터 범위를 명시적으로 지정 합니다. 예를 들어이 함수를 사용 하 여 핫 캐시에 있는 테이블의 데이터로 처리를 제한할 수 있습니다.
