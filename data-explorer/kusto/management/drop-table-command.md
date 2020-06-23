---
title: . drop table 및 drop tables-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 drop table 및 drop table을 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/18/2020
ms.openlocfilehash: 3e1eb57741302d34664f6cd8f256612a6e70bdd1
ms.sourcegitcommit: e87b6cb2075d36dbb445b16c5b83eff7eaf3cdfa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/23/2020
ms.locfileid: "85264490"
---
# <a name="drop-table-and-drop-tables"></a>. drop table 및 drop tables

데이터베이스에서 테이블 또는 여러 테이블을 제거 합니다.

[테이블 관리자 권한이](../management/access-control/role-based-authorization.md)필요 합니다.

> [!NOTE]
> `.drop` `table` 명령은 데이터만 일시 삭제 합니다. 즉, 데이터를 쿼리할 수 없지만 영구적 저장소에서 계속 복구할 수 있습니다. 기본 저장소 아티팩트는 `recoverability` 데이터를 테이블로 수집 때 적용 된 [보존 정책의](../management/retentionpolicy.md) 속성에 따라 하드 삭제 됩니다.

**구문**

`.drop``table` *TableName* [ `ifexists` ]

`.drop``tables`(*TableName1*, *TableName2*,..) [ifexists]

> [!NOTE]
> `ifexists`이 지정 된 경우 존재 하지 않는 테이블이 있으면 명령이 실패 하지 않습니다.

**예제**

```kusto
.drop table CustomersTable ifexists
.drop tables (ProductsTable, ContactsTable, PricesTable) ifexists
```

**반환**

이 명령은 데이터베이스의 나머지 테이블 목록을 반환 합니다.

| 출력 매개 변수 | Type   | Description                             |
|------------------|--------|-----------------------------------------|
| TableName        | String | 테이블의 이름입니다.                  |
| DatabaseName     | String | 테이블이 속한 데이터베이스입니다. |
