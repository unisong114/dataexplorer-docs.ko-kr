---
title: .undo 드롭 테이블 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기에서 .undo 놓기 테이블에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: 55fd08ce2624c522b971e1ee3862f7d11c6f679f
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81519553"
---
# <a name="undo-drop-table"></a>.undo drop table

`.undo` `drop` 명령은 `table` 드롭 테이블 작업을 특정 데이터베이스 버전으로 되돌립니다.

**구문**

`.undo``drop` `version=v` *TableName* `as` *NewTableName* *DB_MajorVersion.DB_MinorVersion* 테이블 이름 [ 새 테이블 이름] DB_MajorVersion.DB_MinorVersion `table`

명령은 데이터베이스 컨텍스트로 실행되어야 합니다.

**반환**

이 명령은 다음을 수행합니다.
* 원래 테이블 익스텐트 목록을 반환합니다.
* 각 익스텐트에 포함된 레코드 수를 지정합니다.
* 복구 작업이 성공했거나 실패한 경우 반환
* 관련된 경우 실패 이유를 반환합니다.

| 익스텐트 ID                             | 숫자Of레코드 | Status                   | 실패 이유                                                                                                                  |
|--------------------------------------|-----------------|--------------------------|--------------------------------------------------------------------------------------------------------------------------------|
| ef296c9e-d75d-44bc-985c-b93d2519691 | 100             | 복구                |
| 370b30d7-cf2a-4997-986e-3d05f49c9689 | 1000            | 복구                |
| 861f18a5-6cde-4f1e-a003-a43506f9e8da | 855             | 범위를 복구할 수 없음 | 익스텐트 컨테이너: 4b47fd84-c7db-4cfb-9378-67c1de7bf154가 발견되지 않았으며, 저장소에서 범위가 제거되어 복원할 수 없습니다. |

**예**

```
// Recover TestTable table to database version 24.3
.undo drop table TestTable version="v24.3"
```

```
// Recover TestTable table to database version 10.3 with new table name, NewTestTable (can be used if a table with the same name was already created since the drop)  
.undo drop table TestTable as NewTestTable version="v10.3"
```

**필요한 데이터베이스 버전을 찾는 방법**

다음 `.show` `journal` 명령을 사용하여 드롭 작업이 실행되기 전에 데이터베이스 버전을 찾을 수 있습니다.

```
.show database TestDB journal | where Event == "DROP-TABLE" and EntityName == "TestTable" | project OriginalEntityVersion 
```

| 오리지널 엔티티버전 |
|-----------------------|
| v24.3                 |

**제한 사항**

이 데이터베이스에서 제거 명령이 실행된 경우 삭제 제거 실행 이전의 버전으로 실행 취소 테이블 명령을 실행할 수 없습니다.

익스텐트는 컨테이너가 상주하는 익스텐트 컨테이너의 하드 삭제 기간에 아직 도달하지 않은 경우에만 복구할 수 있습니다.

명령에데이터베이스 [관리자 권한이](../management/access-control/role-based-authorization.md)필요합니다.