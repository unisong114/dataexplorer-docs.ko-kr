---
title: 캐시 정책 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 캐시 정책에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: ca14703b2548bdb23dc3e6e352aeaacbc17303b4
ms.sourcegitcommit: e94be7045d71a0435b4171ca3a7c30455e6dfa57
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81744477"
---
# <a name="cache-policy"></a>캐시 정책

이 문서에서는 [캐시 정책을](cachepolicy.md) 만들고 변경하는 데 사용되는 명령에 대해 설명합니다. 

## <a name="displaying-the-cache-policy"></a>캐시 정책 표시

정책은 데이터 또는 테이블에 설정할 수 있으며 다음 명령 중 하나를 사용하여 표시됩니다.

* `.show``database` *데이터베이스 이름* `policy``caching`
* `.show``table` *데이터베이스 이름*`.`*테이블 이름* `policy``caching`

## <a name="altering-the-cache-policy"></a>캐시 정책 변경

```kusto
.alter <entity_type> <database_or_table_name> policy caching hot = <timespan>
```

동일한 데이터베이스 컨텍스트에서 여러 테이블에 대한 캐시 정책 변경:

```kusto
.alter tables (table_name [, ...]) policy caching hot = <timespan>
```

캐시 정책:

```kusto
{
  "DataHotSpan": {
    "Value": "3.00:00:00"
  },
  "IndexHotSpan": {
    "Value": "3.00:00:00"
  }
}
```

* `entity_type`: 테이블, 데이터베이스 또는 클러스터
* `database_or_table`: 엔터티가 테이블 또는 데이터베이스인 경우 명령에 이름을 다음과 같이 지정해야 합니다. 
  - `database_name` 또는 
  - `database_name.table_name` 또는 
  - `table_name`(특정 데이터베이스의 컨텍스트에서 실행할 때)

## <a name="deleting-the-cache-policy"></a>캐시 정책 삭제

```kusto
.delete <entity_type> <database_or_table_name> policy caching
```

**예**

데이터베이스의 `MyTable` `MyDatabase`테이블에 대한 캐시 정책 표시:

```kusto
.show table MyDatabase.MyTable policy caching 
```

테이블의 `MyTable` 캐시 정책을 데이터베이스 컨텍스트에서 3일로 설정합니다.

```kusto
.alter table MyTable policy caching hot = 3d
```

데이터베이스 컨텍스트에서 여러 테이블에 대한 정책을 3일로 설정합니다.

```kusto
.alter tables (MyTable1, MyTable2, MyTable3) policy caching hot = 3d
```

테이블에 설정된 정책 삭제:

```kusto
.delete table MyTable policy caching
```

데이터베이스에서 설정된 정책 삭제:

```kusto
.delete database MyDatabase policy caching
```
