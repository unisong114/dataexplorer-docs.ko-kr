---
title: . 테이블 데이터 지우기-Azure 데이터 탐색기
description: 이 문서에서는 `.clear table data` Azure 데이터 탐색기의 명령을 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: vrozov
ms.service: data-explorer
ms.topic: reference
ms.date: 10/01/2020
ms.openlocfilehash: 513544b8fb373f7242bd36b250790801b39061b2
ms.sourcegitcommit: 1618cbad18f92cf0cda85cb79a5cc1aa789a2db7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2020
ms.locfileid: "91615080"
---
# <a name="clear-table-data"></a>. 테이블 데이터 지우기

스트리밍 수집 데이터를 포함 하 여 기존 테이블의 데이터를 지웁니다.

`.clear``table` *TableName*`data` 

> [!NOTE]
> * [테이블 관리자 권한이](../management/access-control/role-based-authorization.md) 필요 합니다.

**예제** 

```kusto
.clear table LyricsAsTable data 
```
 
