---
title: 행 순서 정책 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 RowOrder 정책에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
ms.openlocfilehash: cda4c9a6017071878832fab376a0376d250f3ed6
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81520250"
---
# <a name="roworder-policy"></a>행오더 정책

이 문서에서는 [행 순서 정책을](../management/roworderpolicy.md)만들고 변경하는 데 사용되는 제어 명령에 대해 설명합니다.

## <a name="show-roworder-policy"></a>행순서 정책 표시

```kusto
.show table <table_name> policy roworder

.show table * policy roworder
```

## <a name="delete-roworder-policy"></a>행순서 삭제 정책

```kusto
.delete table <table_name> policy roworder
```

## <a name="alter-roworder-policy"></a>행순서 정책 변경

```kusto
.alter table <table_name> policy roworder (<row_order_policy>)

.alter tables (<table_name> [, ...]) policy roworder (<row_order_policy>)

.alter-merge table <table_name> policy roworder (<row_order_policy>)
```

**예**

다음 예제에서는 `TenantId` 열(오름차순)에 있는 행 순서 정책을 기본 키로 설정하고 `Timestamp` 열(오름차순)에 보조 키로 설정합니다. 그런 다음 정책을 쿼리합니다.

```kusto
.alter table events policy roworder (TenantId asc, Timestamp desc)

.alter tables (events1, events2, events3) policy roworder (TenantId asc, Timestamp desc)

.show table events policy roworder 
```

|TableName|행 순서 정책| 
|---|---|
|events|(테넌트ID asc, 타임스탬프 desc)| 