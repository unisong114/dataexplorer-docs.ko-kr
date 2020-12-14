---
title: Kusto IngestionBatching 정책 관리 명령-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 IngestionBatching policy 명령에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: 2ff3578b055fd487f3d339dc9b7fb4aa1ad11e14
ms.sourcegitcommit: d9e203a54b048030eeb6d05b01a65902ebe4e0b8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/14/2020
ms.locfileid: "97371444"
---
# <a name="ingestionbatching-policy-command"></a>IngestionBatching policy 명령

[IngestionBatching 정책은](batchingpolicy.md) 지정 된 설정에 따라 데이터를 수집 하는 동안 데이터 집계가 중지 되는 시기를 결정 하는 정책 개체입니다.

정책을로 설정할 수 있습니다 .이 경우에는 기본값을 사용 하 여 `null` 최대 일괄 처리 시간 범위를 5 분, 1000 항목 및 전체 일괄 처리 크기인 1g로 설정 하거나 Kusto로 설정 된 기본 클러스터 값으로 설정 합니다.

특정 엔터티에 대해 정책이 설정 되지 않은 경우에는 더 높은 계층 구조 수준 정책을 검색 합니다. 모두 null로 설정 된 경우에는 기본값이 사용 됩니다. 

**IngestionBatching 제한:**

| Type | 기본값 | 최소 | 최대
|---|---|---|---|
| 항목 수 | 1000 | 1 | 2000 |
| 데이터 크기 (MB) | 1000 | 100 | 1000 |
| 시간 | 5분 | 10초 | 15분 |

## <a name="displaying-the-ingestionbatching-policy"></a>IngestionBatching 정책 표시

이 정책은 데이터베이스 또는 테이블에 대해 설정할 수 있으며 다음 명령 중 하나를 사용 하 여 표시 됩니다.

* `.show``database`  DatabaseName `policy``ingestionbatching`
* `.show``table` *DatabaseName* `.`  TableName `policy``ingestionbatching`

## <a name="altering-the-ingestionbatching-policy"></a>IngestionBatching 정책 변경

```kusto
.alter <entity_type> <database_or_table_name> policy ingestionbatching @'<ingestionbatching policy json>'
```

여러 테이블에 대 한 IngestionBatching 정책 변경 (동일한 데이터베이스 컨텍스트에서):

```kusto
.alter tables (table_name [, ...]) policy ingestionbatching @'<ingestionbatching policy json>'
```

IngestionBatching 정책:

```kusto
{
  "MaximumBatchingTimeSpan": "00:05:00",
  "MaximumNumberOfItems": 500, 
  "MaximumRawDataSizeMB": 1024
}
```

* `entity_type` : 테이블, 데이터베이스
* `database_or_table`: 엔터티가 테이블 또는 데이터베이스인 경우 다음과 같이 명령에 이름을 지정 해야 합니다. 
  - `database_name` 또는 
  - `database_name.table_name` 또는 
  - `table_name` (특정 데이터베이스의 컨텍스트에서 실행 하는 경우)

## <a name="deleting-the-ingestionbatching-policy"></a>IngestionBatching 정책을 삭제 하는 중

```kusto
.delete <entity_type> <database_or_table_name> policy ingestionbatching
```

**예**

```kusto
// Show IngestionBatching policy for table `MyTable` in database `MyDatabase`
.show table MyDatabase.MyTable policy ingestionbatching 

// Set IngestionBatching policy on table `MyTable` (in database context) to batch ingress data by 30 seconds, 500 files, or 1GB (whatever comes first)
.alter table MyTable policy ingestionbatching @'{"MaximumBatchingTimeSpan":"00:00:30", "MaximumNumberOfItems": 500, "MaximumRawDataSizeMB": 1024}'

// Set IngestionBatching policy on multiple tables (in database context) to batch ingress data by 1 minute, 20 files, or 300MB (whatever comes first)
.alter tables (MyTable1, MyTable2, MyTable3) policy ingestionbatching @'{"MaximumBatchingTimeSpan":"00:01:00", "MaximumNumberOfItems": 20, "MaximumRawDataSizeMB": 300}'

// Delete IngestionBatching policy on table `MyTable`
.delete table MyTable policy ingestionbatching

// Delete IngestionBatching policy on database `MyDatabase`
.delete database MyDatabase policy ingestionbatching
```
