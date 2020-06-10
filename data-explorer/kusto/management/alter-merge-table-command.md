---
title: . alter-merge table-Azure 데이터 탐색기
description: 이 문서에서는. alter-merge 테이블 명령을 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 06/08/2020
ms.openlocfilehash: cc4002d9af8b18841714ac9f91809fb18274782f
ms.sourcegitcommit: be1bbd62040ef83c08e800215443ffee21cb4219
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/10/2020
ms.locfileid: "84670517"
---
# <a name="alter-merge-table"></a>. alter-merge 테이블
 
`.alter-merge table` 명령은 다음을 수행합니다.

* 기존 열의 데이터를 보호 합니다.
* `docstring`기존 테이블에 새 열, 및 폴더를 추가 합니다.
* 테이블 이름의 범위를 지정 하는 특정 데이터베이스의 컨텍스트에서 실행 해야 합니다.
* [테이블 관리자 권한이](../management/access-control/role-based-authorization.md) 필요 합니다.

> [!WARNING]
> `.alter-merge`명령을 잘못 사용 하면 데이터가 손실 될 수 있습니다.

> [!TIP]
> 에는 `.alter-merge` `.alter` 유사한 기능을 포함 하는 테이블 명령의 대응 항목이 있습니다. 자세한 내용은 [alter table을 참조 하세요.](../management/alter-table-command.md)

**구문**

`.alter-merge``table` *TableName* (*columnName*:*columnType*, ...)  [ `with` `(` [ `docstring` `=` *설명서*] [ `,` `folder` `=` *FolderName*] `)` ]

테이블 열을 지정 합니다.
 * 존재 하지 않으며 지정 하는 열은 기존 스키마의 끝에 추가 됩니다.
 * 전달 된 스키마에 일부 테이블 열이 포함 되어 있지 않으면 열이 삭제 되지 않습니다.
 * 다른 유형의 기존 열을 지정 하면 명령이 실패 합니다.

> [!TIP]
> `.show table [TableName] cslschema`을 사용 하 여 기존 열 스키마를 변경 하기 전에 가져올 수 있습니다.

명령은 데이터에 어떤 영향을 미칩니까?
* 기존 데이터는 명령에 의해 물리적으로 수정 되지 않습니다. 제거 된 열의 데이터는 무시 됩니다. 새 열의 데이터는 null로 간주 됩니다.
* 클러스터가 구성 된 방식에 따라 데이터 수집은 사용자 조작이 없어도 테이블의 열 스키마를 수정할 수 있습니다. 테이블의 열 스키마를 변경 하는 경우 수집 시 명령이 제거 되는 필요한 열이 추가 되지 않도록 합니다.

**예**

```kusto
.alter-merge table MyTable (ColumnX:string, ColumnY:int) 
.alter-merge table MyTable (ColumnX:string, ColumnY:int) with (docstring = "Some documentation", folder = "Folder1")
```
 
