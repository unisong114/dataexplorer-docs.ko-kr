---
title: . alter table 및. alter-merge table-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 alter table 및. alter-merge 테이블에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/17/2020
ms.openlocfilehash: 18b0502754e95ca56d6a7f6946b9330bd42b174a
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/01/2020
ms.locfileid: "82616442"
---
# <a name="alter-table-and-alter-merge-table"></a>. alter table 및. alter-merge table

이 `.alter table` 명령은 새 열 스키마, docstring 및 폴더를 기존 테이블로 설정 하 고 기존 열 스키마, docstring 및 폴더를 재정의 합니다. 명령에 의해 "유지" 되는 기존 열의 데이터는 유지 됩니다. 예를 들어이 명령을 사용 하 여 테이블의 열을 다시 정렬할 수 있습니다.

이 `.alter-merge table` 명령은 새 열 docstring 및 폴더를 기존 테이블에 추가 합니다.
기존 열의 데이터는 그대로 유지 됩니다.

두 명령은 모두 테이블 이름의 범위를 지정 하는 특정 데이터베이스의 컨텍스트에서 실행 해야 합니다.

[테이블 관리자 권한이](../management/access-control/role-based-authorization.md)필요 합니다.

**구문**

`.alter``table` *TableName* (*columnName*:*columnType*, ...)  [`with` `(`[`docstring` `=` *FolderName* *Documentation*`,` 설명서] [ `folder` FolderName] `)`] `=`

`.alter-merge``table` *TableName* (*columnName*:*columnType*, ...)  [`with` `(`[`docstring` `=` *FolderName* *Documentation*`,` 설명서] [ `folder` FolderName] `)`] `=`

성공적으로 완료 된 후 테이블에 있어야 하는 열을 지정 합니다. 

> [!WARNING]
> `.alter` 명령을 잘못 사용 하면 데이터가 손실 될 수 있습니다.
> `.alter` 와 `.alter-merge` 아래의 차이점을 주의 깊게 읽으십시오.

`.alter-merge`:

 * 존재 하지 않으며 지정 하는 열이 기존 스키마의 끝에 추가 됩니다.
 * 전달 된 스키마에 일부 테이블 열이 포함 되어 있지 않으면 해당 스키마는 삭제 되지 않습니다.
 * 다른 유형의 기존 열을 지정한 경우에는 명령이 실패 합니다.

`.alter`만 (아님 `.alter-merge`):

 * 테이블에는 지정 된 것과 동일한 순서로 동일한 열이 있습니다.
 * 명령에 지정 되지 않은 기존 열은에서 `.drop column`와 같이 삭제 되 고 데이터의 데이터가 손실 됩니다.
 * 테이블을 변경할 때는 열 유형 변경은 지원 되지 않습니다. 대신 [. 열 변경](alter-column.md) 명령을 사용 합니다.

> [!TIP] 
> 을 `.show table [TableName] cslschema` 사용 하 여 기존 열 스키마를 변경 하기 전에 가져올 수 있습니다. 

다음은 두 명령에 모두 적용 됩니다.

1. 기존 데이터는 이러한 명령으로 실제로 수정 되지 않습니다. 제거 된 열의 데이터는 무시 됩니다. 새 열의 데이터는 null로 간주 됩니다.
1. 클러스터가 구성 된 방식에 따라 데이터 수집은 사용자 조작이 없어도 테이블의 열 스키마를 수정할 수 있습니다. 따라서 테이블의 열 스키마를 변경 하는 경우 수집 시 명령이 제거 되는 필요한 열이 추가 되지 않도록 해야 합니다.

> [!WARNING]
> `.alter table` 명령을 사용 하 여 병렬로 발생 하는 테이블의 열 스키마를 수정 하는 테이블에 대 한 데이터 수집 프로세스는 테이블 열의 순서와는 독립적으로 수행 될 수 있습니다. 데이터를 잘못 된 열로 수집 하는 위험도 있습니다. 이러한 명령을 실행 하는 동안 수집을 중지 하거나 이러한 수집 작업에서 항상 매핑 개체를 사용 하도록 하 여이를 방지 합니다.

**예**

```kusto
.alter table MyTable (ColumnX:string, ColumnY:int) 
.alter table MyTable (ColumnX:string, ColumnY:int) with (docstring = "Some documentation", folder = "Folder1")
.alter-merge table MyTable (ColumnX:string, ColumnY:int) 
.alter-merge table MyTable (ColumnX:string, ColumnY:int) with (docstring = "Some documentation", folder = "Folder1")
```