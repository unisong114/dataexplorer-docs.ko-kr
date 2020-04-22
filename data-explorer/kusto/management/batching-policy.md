---
title: 인시션 배치 정책 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 인시션 배치 정책에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: 232767e390669a08312f10d3999d19264fb29f26
ms.sourcegitcommit: e94be7045d71a0435b4171ca3a7c30455e6dfa57
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81744311"
---
# <a name="ingestionbatching-policy"></a>인제션배치 정책

[인비많이팅 정책은](batchingpolicy.md) 지정된 설정에 따라 데이터를 수집하는 동안 데이터 집계를 중지해야 하는 시기를 결정하는 정책 개체입니다.

이 경우 기본값을 `null`사용하여 최대 일괄 처리 시간 범위를 5분, 1000개 항목, 총 일괄 처리 크기 1G 또는 Kusto가 설정한 기본 클러스터 값으로 설정할 수 있습니다.

정책이 특정 엔터티에 대해 설정되지 않은 경우 모든 정책이 기본값을 null로 설정하면 더 높은 계층 수준 정책을 찾습니다. 

이 정책의 제한은 10초로 낮으며 15분을 초과하는 값을 사용하지 않는 것이 좋습니다.

## <a name="displaying-the-ingestionbatching-policy"></a>인시션 일괄 처리 정책 표시

정책은 데이터베이스 또는 테이블에 설정할 수 있으며 다음 명령 중 하나를 사용하여 표시됩니다.

* `.show``database` *데이터베이스 이름* `policy``ingestionbatching`
* `.show``table` *데이터베이스 이름*`.`*테이블 이름* `policy``ingestionbatching`

## <a name="altering-the-ingestionbatching-policy"></a>인시션 일괄 처리 정책 변경

```kusto
.alter <entity_type> <database_or_table_name> policy ingestionbatching @'<ingestionbatching policy json>'
```

동일한 데이터베이스 컨텍스트에서 여러 테이블에 대한 인시션 배치 정책 변경:

```kusto
.alter tables (table_name [, ...]) policy ingestionbatching @'<ingestionbatching policy json>'
```

인제션배치 정책:

```kusto
{
  "MaximumBatchingTimeSpan": "00:05:00",
  "MaximumNumberOfItems": 500, 
  "MaximumRawDataSizeMB": 1024
}
```

* `entity_type`: 표, 데이터베이스
* `database_or_table`: 엔터티가 테이블 또는 데이터베이스인 경우 명령에 이름을 다음과 같이 지정해야 합니다. 
  - `database_name` 또는 
  - `database_name.table_name` 또는 
  - `table_name`(특정 데이터베이스의 컨텍스트에서 실행할 때)

## <a name="deleting-the-ingestionbatching-policy"></a>인시션 일괄 처리 정책 삭제

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
