---
title: .drop 테이블 및 .drop 테이블 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기에서 .drop 테이블 및 .drop 테이블에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/18/2020
ms.openlocfilehash: 5f3a488aba5a6785ceb6ad4a093c520ec0509e5e
ms.sourcegitcommit: e94be7045d71a0435b4171ca3a7c30455e6dfa57
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81744746"
---
# <a name="drop-table-and-drop-tables"></a>.drop 테이블 및 .drop 테이블

데이터베이스에서 테이블(또는 여러 테이블)을 제거합니다.

[테이블 관리자 권한이](../management/access-control/role-based-authorization.md)필요합니다.

> [!NOTE]
> `.drop` 명령은 소프트만 데이터를 삭제합니다(즉, 데이터를 쿼리할 수는 없지만 영구 저장소에서 복구할 수 있음). `table` 기본 저장소 아티팩트는 데이터가 테이블에 `recoverability` 수집될 때 적용된 [보존 정책의](../management/retentionpolicy.md) 속성에 따라 하드 삭제됩니다.

**구문**

`.drop``table` *테이블* 이름`ifexists`[ ]

`.drop``tables` *(표이름1,* *표명2,..)* [존재하는 경우]

> [!NOTE]
> `ifexists` 지정하면 존재하지 않는 테이블이 있는 경우 명령이 실패하지 않습니다.

**예제**

```kusto
.drop table CustomersTable ifexists
.drop tables (ProductsTable, ContactsTable, PricesTable) ifexists
```

**반환**

이 명령은 데이터베이스에 남아 있는 테이블 목록을 반환합니다. 

| 출력 매개 변수 | Type   | Description                             |
|------------------|--------|-----------------------------------------|
| TableName        | String | 테이블의 이름입니다.                  |
| DatabaseName     | String | 테이블이 속한 데이터베이스입니다. |