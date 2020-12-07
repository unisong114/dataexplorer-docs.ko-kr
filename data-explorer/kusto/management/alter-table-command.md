---
title: . alter table-Azure 데이터 탐색기
description: 이 문서에서는. alter table 명령을 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: reference
ms.date: 06/08/2020
ms.openlocfilehash: ccefca3d3cbf1f97661fead54bbc3cfaf207ca1a
ms.sourcegitcommit: 80f0c8b410fa4ba5ccecd96ae3803ce25db4a442
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/30/2020
ms.locfileid: "96321678"
---
# <a name="alter-table"></a>.alter table
 
`.alter table` 명령은 다음을 수행합니다.
* "유지" 열의 데이터를 보호 합니다.
* 테이블 열 다시 정렬
* 새 열 스키마, `docstring` 및 폴더를 기존 테이블로 설정 하 여 기존 열 스키마, 및 폴더를 덮어씁니다 `docstring` .
* 테이블 이름의 범위를 지정 하는 특정 데이터베이스의 컨텍스트에서 실행 해야 합니다.
* [테이블 관리자 권한이](../management/access-control/role-based-authorization.md) 필요 합니다.

> [!WARNING]
> `.alter`명령을 잘못 사용 하면 데이터가 손실 될 수 있습니다.

> [!TIP]
> 에는 `.alter` `.alter-merge` 유사한 기능을 포함 하는 테이블 명령의 대응 항목이 있습니다. 자세한 내용은 [`.alter-merge table`](../management/alter-merge-table-command.md)를 참조하세요.

**구문**

`.alter``table` *TableName* (*columnName*:*columnType*, ...)  [ `with` `(` [ `docstring` `=` *설명서*] [ `,` `folder` `=` *FolderName*] `)` ]


 * 테이블에는 지정 된 것과 동일한 순서로 동일한 열이 있습니다.
 테이블 열을 지정 합니다.
 * 명령에 기존 열이 지정 되지 않은 경우에는 해당 열이 삭제 되 고 명령 에서처럼 데이터의 데이터가 손실 됩니다 `.drop column` .
 * 테이블을 변경 하는 경우 열 유형 변경은 지원 되지 않습니다. [`.alter column`](alter-column.md)대신 명령을 사용 합니다.

> [!TIP]
> `.show table [TableName] cslschema`을 사용 하 여 기존 열 스키마를 변경 하기 전에 가져올 수 있습니다.


명령은 데이터에 어떤 영향을 미칩니까?
* 기존 데이터는 명령에 의해 물리적으로 수정 되지 않습니다. 제거 된 열의 데이터는 무시 됩니다. 새 열의 데이터는 null로 간주 됩니다.
* 클러스터가 구성 된 방식에 따라 데이터 수집은 사용자 조작이 없어도 테이블의 열 스키마를 수정할 수 있습니다. 테이블의 열 스키마를 변경 하는 경우 수집 시 명령이 제거 되는 필요한 열이 추가 되지 않도록 합니다.

> [!WARNING]
> 테이블의 열 스키마를 수정 하는 테이블에 대 한 데이터 수집 프로세스는 명령과 병렬로 발생 하며 `.alter table` 테이블 열의 순서와는 독립적으로 수행 될 수 있습니다. 데이터를 잘못 된 열로 수집 하는 위험도 있습니다. 명령 중에 수집을 중지 하거나 이러한 수집 작업에서 항상 매핑 개체를 사용 하도록 설정 하 여 이러한 문제를 방지 합니다.

**예**

```kusto
.alter table MyTable (ColumnX:string, ColumnY:int) 
.alter table MyTable (ColumnX:string, ColumnY:int) with (docstring = "Some documentation", folder = "Folder1")
```
