---
title: .alter 테이블 및 .alter-merge 테이블 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기에서 .alter 테이블 및 .alter 병합 테이블에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/17/2020
ms.openlocfilehash: 516c5c7b85f7c310188fd11ae24e52cb23423143
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81522375"
---
# <a name="alter-table-and-alter-merge-table"></a>.alter 테이블 및 .alter-merge 테이블

이 `.alter table` 명령은 기존 열 스키마, 문서 문자열 및 폴더를 재정의하여 새 열 스키마, 문서 문자열 및 폴더를 기존 테이블로 설정합니다. 명령에 의해 "보존"되는 기존 열의 데이터는 보존됩니다(예: 이 명령을 사용하여 테이블의 열 순서를 다시 지정할 수 있음).

이 `.alter-merge table` 명령은 기존 테이블에 새 열, 문서 문자열 및 폴더를 추가합니다.
기존 열의 데이터는 유지됩니다.

두 명령은 테이블 이름을 범위 지정하는 특정 데이터베이스의 컨텍스트에서 실행되어야 합니다.

[테이블 관리자 권한이](../management/access-control/role-based-authorization.md)필요합니다.

**구문**

`.alter``table` *테이블 이름* *(열 이름*:*열유형*, ...)  [`with` `(``docstring` `=` [ [`,` `folder` `=` *문서* `)`] [ 폴더 *이름*] ]

`.alter-merge``table` *테이블 이름* *(열 이름*:*열유형*, ...)  [`with` `(``docstring` `=` [ [`,` `folder` `=` *문서* `)`] [ 폴더 *이름*] ]

성공적으로 완료된 후 테이블에 있어야 하는 열을 지정합니다. 

> [!WARNING]
> `.alter` 명령을 잘못 사용하면 데이터가 손실될 수 있습니다.
> 아래의 차이점을 `.alter` 주의 `.alter-merge` 깊게 읽으십시오.

`.alter-merge`:

 * 존재하지 않으며 지정한 열은 기존 스키마의 끝에 추가됩니다.
 * 전달된 스키마에 일부 테이블 열이 포함되어 있지 않으면 삭제되지 않습니다.
 * 다른 유형의 기존 열을 지정하면 명령이 실패합니다.

`.alter`만 (안됨): `.alter-merge`

 * 테이블에는 지정된 순서와 동일한 열이 있습니다.
 * 명령에 지정되지 않은 기존 열은 삭제되고(에서와 `.drop column`같이) 해당 열의 데이터가 손실됩니다.
 * 테이블을 변경할 때 열 형식을 변경하는 것은 지원되지 않습니다. 대신 [.alter 열](alter-column.md) 명령을 사용합니다.

> [!TIP] 
> 기존 `.show table [TableName] cslschema` 열 스키마를 변경하기 전에 사용하는 데 사용합니다. 

다음은 두 명령에 모두 적용됩니다.

1. 기존 데이터는 이러한 명령에 의해 물리적으로 수정되지 않습니다. 제거된 열의 데이터는 무시됩니다. 새 열의 데이터는 null로 가정됩니다.
1. 클러스터를 구성하는 방법에 따라 데이터 수집은 사용자 상호 작용없이도 테이블의 열 스키마를 수정할 수 있습니다. 따라서 테이블의 열 스키마를 변경할 때 인레이션으로 인해 명령이 제거할 필요한 열이 추가되지 않도록 합니다.

> [!WARNING]
> `.alter table` 명령과 병렬로 발생하는 테이블의 열 스키마를 수정하는 테이블로의 데이터 수집 프로세스는 테이블 열의 순서에 구애받지 않고 수행될 수 있습니다. 또한 데이터가 잘못된 열로 수집될 위험이 있습니다. 이러한 명령 중에 인슈어링을 중지하거나 이러한 섭취 작업이 항상 매핑 개체를 사용하도록 하여 이를 방지합니다.

**예**

```
.alter table MyTable (ColumnX:string, ColumnY:int) 
.alter table MyTable (ColumnX:string, ColumnY:int) with (docstring = "Some documentation", folder = "Folder1")
.alter-merge table MyTable (ColumnX:string, ColumnY:int) 
.alter-merge table MyTable (ColumnX:string, ColumnY:int) with (docstring = "Some documentation", folder = "Folder1")
```