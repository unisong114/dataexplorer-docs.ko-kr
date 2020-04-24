---
title: 보존 정책 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 보존 정책에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: b0366bef619d815bbe58f91730eff70ec847c239
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81520352"
---
# <a name="retention-policy"></a>보존 정책

이 문서에서는 [보존 정책을](retentionpolicy.md)만들고 변경하는 데 사용되는 제어 명령에 대해 설명합니다.

## <a name="show-retention-policy"></a>보존 정책 표시

```kusto
.show <entity_type> <database_or_table> policy retention

.show <entity_type> *  policy retention
```

* `entity_type`: 표 또는 데이터베이스
* `database_or_table`: `database_name` `database_name.table_name` 또는 `table_name` (데이터베이스 컨텍스트에서)

**예제**

다음이라는 `MyDatabase`데이터베이스에 대한 보존 정책을 표시합니다.

```kusto
.show database MyDatabase policy retention
```

## <a name="delete-retention-policy"></a>보존 정책 삭제

데이터 보존 정책을 삭제하는 것은 데이터 보존을 무제한으로 설정하는 것입니다.

테이블의 데이터 보존 정책을 삭제하면 테이블이 데이터베이스 수준에서 보존 정책을 파생합니다.

```kusto
.delete <entity_type> <database_or_table> policy retention
```

* `entity_type`: 표 또는 데이터베이스
* `database_or_table`: `database_name` `database_name.table_name` 또는 `table_name` (데이터베이스 컨텍스트에서)

**예제**

다음이라는 `MyTable1`테이블의 보존 정책을 삭제합니다.

```kusto
.delete table MyTable policy retention
```


## <a name="alter-retention-policy"></a>보존 정책 변경

```kusto
.alter <entity_type> <database_or_table> policy retention <retention_policy>

.alter tables (<table_name> [, ...]) policy retention <retention_policy>

.alter-merge <entity_type> <database_or_table> policy retention <retention_policy>

.alter-merge <entity_type> <database_or_table_name> policy retention [softdelete = <timespan>] [recoverability = disabled|enabled]
```

* `entity_type`: 표 또는 데이터베이스
* `database_or_table`: `database_name` `database_name.table_name` 또는 `table_name` (데이터베이스 컨텍스트에서)
* `table_name`: 데이터베이스 컨텍스트에서 테이블의 이름입니다.  와일드카드(여기서`*` 허용됨).
* `retention_policy` :

```
    "{ 
        \"SoftDeletePeriod\": \"10.00:00:00\", \"Recoverability\": \"Disabled\"
    }" 
```

**예**

다음이라는 `MyDatabase`데이터베이스에 대한 보존 정책을 표시합니다.

```kusto
.show database MyDatabase policy retention
```

10일의 일시 삭제 기간 및 데이터 복구 가능성을 사용하여 보존 정책을 설정합니다.

```kusto
.alter-merge table Table1 policy retention softdelete = 10d recoverability = disabled
```

10일의 일시 삭제 기간과 활성화된 데이터 복구 기능을 사용하여 보존 정책을 설정합니다.

```kusto
.alter table Table1 policy retention "{\"SoftDeletePeriod\": \"10.00:00:00\", \"Recoverability\": \"Enabled\"}"
```

위와 동일한 보존 정책을 설정하지만 이번에는 여러 테이블(표1, 표2 및 표3)에 대해 다음을 설정합니다.

```kusto
.alter tables (Table1, Table2, Table3) policy retention "{\"SoftDeletePeriod\": \"10.00:00:00\", \"Recoverability\": \"Enabled\"}"
```

기본값: 100년 동안의 일시 삭제 기간 및 복구 가능성을 사용하도록 설정하여 보존 정책을 설정합니다.

```kusto
.alter table Table1 policy retention "{}"
```