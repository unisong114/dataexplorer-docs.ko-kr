---
title: .alter 테이블 폴더 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 .alter 테이블 폴더에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/06/2020
ms.openlocfilehash: a1d368d50f0e42acdbc25348b8025fbe8b530536
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81522307"
---
# <a name="alter-table-folder"></a>.alter table folder

기존 테이블의 폴더 값을 변경합니다. 

`.alter``table` *테이블 이름* `folder` *폴더*

> [!NOTE]
> * [데이터베이스 관리자 권한이](../management/access-control/role-based-authorization.md) 필요합니다.
> * 테이블을 처음 만든 [데이터베이스 사용자도](../management/access-control/role-based-authorization.md) 테이블을 편집할 수 있습니다.
> * 테이블이 없으면 오류가 반환됩니다. 새 테이블을 만들려면 [.create 테이블을 참조하십시오.](create-table-command.md)

**예** 

```
.alter table MyTable folder "Updated folder"
```

```
.alter table MyTable folder @"First Level\Second Level"
```