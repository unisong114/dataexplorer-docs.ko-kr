---
title: 캐시 정책-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 캐시 정책을 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: 9b080badd2dc1015319e9b6d44c4c477061f92f9
ms.sourcegitcommit: 041272af91ebe53a5d573e9902594b09991aedf0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91452717"
---
# <a name="cache-policy-command"></a>캐시 정책 명령

이 문서에서는 [캐시 정책을](cachepolicy.md) 만들고 변경 하는 데 사용 되는 명령을 설명 합니다. 

## <a name="displaying-the-cache-policy"></a>캐시 정책 표시

이 정책은 데이터베이스, 테이블 또는 [구체화 된 뷰에서](materialized-views/materialized-view-overview.md)설정할 수 있으며 다음 명령 중 하나를 사용 하 여 표시 됩니다.

* `.show``database` *DatabaseName* DatabaseName `policy``caching`
* `.show``table` *TableName* TableName `policy``caching`
* `.show``materialized-view` *MaterializedViewName* MaterializedViewName `policy``caching`

## <a name="altering-the-cache-policy"></a>캐시 정책 변경

```kusto
.alter <entity_type> <database_or_table_or_materialized-view_name> policy caching hot = <timespan>
```

여러 테이블에 대 한 캐시 정책 변경 (동일한 데이터베이스 컨텍스트에서):

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

* `entity_type` : 테이블, 데이터베이스 또는 클러스터
* `database_or_table_or_materialized-view`: 엔터티가 테이블 또는 데이터베이스인 경우 다음과 같이 명령에 이름을 지정 해야 합니다. 
  - `database_name` 또는 
  - `database_name.table_name` 또는 
  - `table_name` (특정 데이터베이스의 컨텍스트에서 실행 하는 경우)

## <a name="deleting-the-cache-policy"></a>캐시 정책을 삭제 하는 중

```kusto
.delete <entity_type> <database_or_table_name> policy caching
```

**예**

데이터베이스의 테이블에 대 한 캐시 정책 표시 `MyTable` `MyDatabase` :

```kusto
.show table MyDatabase.MyTable policy caching 
```

테이블 `MyTable` (데이터베이스 컨텍스트)의 캐시 정책을 3 일로 설정:

```kusto
.alter table MyTable policy caching hot = 3d
.alter materialized-view MyMaterializedView policy caching hot = 3d
```

여러 테이블에 대 한 정책 (데이터베이스 컨텍스트)을 3 일로 설정:

```kusto
.alter tables (MyTable1, MyTable2, MyTable3) policy caching hot = 3d
```

테이블에 설정 된 정책 삭제:

```kusto
.delete table MyTable policy caching
```

구체화 된 뷰에서 정책 집합 삭제:

```kusto
.delete materialized-view MyMaterializedView policy caching
```

데이터베이스에 설정 된 정책 삭제:

```kusto
.delete database MyDatabase policy caching
```
