---
title: . 테이블 삭제 취소-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기에서 테이블 삭제 취소에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: 2682afaa9e589a8787b7e42a83e3bb68a24a2781
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/01/2020
ms.locfileid: "82616680"
---
# <a name="undo-drop-table"></a>.undo drop table

`.undo` `drop` 이 `table` 명령은 drop table 작업을 특정 데이터베이스 버전으로 되돌립니다.

**구문**

`.undo``drop` `as` *NewTableName* `version=v` *DB_MajorVersion.DB_MinorVersion* *TableName* TableName [newtablename] DB_MajorVersion DB_MinorVersion `table`

데이터베이스 컨텍스트를 사용 하 여 명령을 실행 해야 합니다.

**반환**

이 명령은 다음을 수행합니다.
* 원래 테이블 익스텐트 목록을 반환 합니다.
* 범위에 포함 되는 레코드 수를 각 익스텐트에 대해 지정 합니다.
* 복구 작업이 성공 또는 실패 한 경우를 반환 합니다.
* 관련 된 오류의 원인을 반환 합니다.

| ExtentId                             | NumberOfRecords | 상태                   | FailureReason                                                                                                                  |
|--------------------------------------|-----------------|--------------------------|--------------------------------------------------------------------------------------------------------------------------------|
| ef296c9e-d75d-44bc-985c-b93dd2519691 | 100             | 차단                |
| 370b30d7-cf2a-4997-986e-3d05f49c9689 | 1000            | 차단                |
| 861f18a5-6cde-4f1e-a003-a43506f9e8da | 855             | 익스텐트를 복구할 수 없음 | 익스텐트 컨테이너: 4b47fd84-c7db-4cfb-9378-67c1de7bf154를 찾을 수 없습니다. 익스텐트가 저장소에서 제거 되었으므로 복원할 수 없습니다. |

**예제**

```kusto
// Recover TestTable table to database version 24.3
.undo drop table TestTable version="v24.3"
```

```kusto
// Recover TestTable table to database version 10.3 with new table name, NewTestTable (can be used if a table with the same name was already created since the drop)  
.undo drop table TestTable as NewTestTable version="v10.3"
```

**필요한 데이터베이스 버전을 찾는 방법**

`journal` 명령을 사용 하 `.show` 여 drop 작업을 실행 하기 전에 데이터베이스 버전을 찾을 수 있습니다.

```kusto
.show database TestDB journal | where Event == "DROP-TABLE" and EntityName == "TestTable" | project OriginalEntityVersion 
```

| OriginalEntityVersion |
|-----------------------|
| v 24.3                 |

**제한 사항**

이 데이터베이스에서 제거 명령을 실행 한 경우 삭제 취소 테이블 명령을 이전 버전의 제거 실행으로 실행할 수 없습니다.

익스텐트는 상주 하는 컨테이너의 하드 삭제 기간이 아직 도달 하지 않은 경우에만 복구할 수 있습니다.

명령에는 [데이터베이스 관리자 권한이](../management/access-control/role-based-authorization.md)필요 합니다.
