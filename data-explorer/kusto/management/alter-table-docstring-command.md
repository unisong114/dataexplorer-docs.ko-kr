---
title: . alter table docstring-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 alter table docstring에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: 790cd9805be6dd4440ef2eb51c504044dc069b3c
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/01/2020
ms.locfileid: "82617785"
---
# <a name="alter-table-docstring"></a>.alter table docstring

기존 테이블의 DocString 값을 변경 합니다.

`.alter``table` *TableName* TableName `docstring` *설명서*

> [!NOTE]
> * [데이터베이스 관리자 권한이](../management/access-control/role-based-authorization.md) 필요 합니다.
> * 테이블을 만든 [데이터베이스 사용자](../management/access-control/role-based-authorization.md) 도 테이블을 수정할 수 있습니다.
> * 테이블이 없으면 오류가 반환 됩니다. 새 테이블을 만들려면를 참조 하십시오 [. create table](create-table-command.md)

**예제** 

```kusto
.alter table LyricsAsTable docstring "This is the theme to Garry's show"
```