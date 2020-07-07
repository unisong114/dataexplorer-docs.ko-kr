---
title: RowOrder 정책-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 RowOrder 정책에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
ms.openlocfilehash: bf8844a72d2b4fc3d9dec4f24fdfa6ac36ee84d7
ms.sourcegitcommit: b08b1546122b64fb8e465073c93c78c7943824d9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/06/2020
ms.locfileid: "85967505"
---
# <a name="roworder-policy-command"></a>roworder policy 명령

이 문서에서는 [행 순서 정책을](../management/roworderpolicy.md)만들고 변경 하는 데 사용 되는 제어 명령을 설명 합니다.

## <a name="show-roworder-policy"></a>RowOrder 정책 표시

```kusto
.show table <table_name> policy roworder

.show table * policy roworder
```

## <a name="delete-roworder-policy"></a>RowOrder 정책 삭제

```kusto
.delete table <table_name> policy roworder
```

## <a name="alter-roworder-policy"></a>Alter RowOrder policy

```kusto
.alter table <table_name> policy roworder (<row_order_policy>)

.alter tables (<table_name> [, ...]) policy roworder (<row_order_policy>)

.alter-merge table <table_name> policy roworder (<row_order_policy>)
```

**예** 

다음 예에서는 `TenantId` 열 (오름차순)의 행 순서 정책을 기본 키로, `Timestamp` 열 (오름차순)을 보조 키로 설정 합니다. 그런 다음 정책을 쿼리 합니다.

```kusto
.alter table events policy roworder (TenantId asc, Timestamp desc)

.alter tables (events1, events2, events3) policy roworder (TenantId asc, Timestamp desc)

.show table events policy roworder 
```

|TableName|RowOrderPolicy| 
|---|---|
|events|(TenantId asc, Timestamp desc)|
