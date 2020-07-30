---
title: cursor_current (), current_cursor ()-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기에서 cursor_current (), current_cursor ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 12/10/2019
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 879fe4aac2a6714f3d7ab16a63c69cd8215bbb04
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348601"
---
# <a name="cursor_current-current_cursor"></a>cursor_current(), current_cursor()

::: zone pivot="azuredataexplorer"

범위에 있는 데이터베이스 커서의 현재 값을 검색 합니다. 이름 및는 `cursor_current` `current_cursor` 동의어입니다.

## <a name="syntax"></a>Syntax

`cursor_current()`

## <a name="returns"></a>반환

`string`범위에 있는 데이터베이스 커서의 현재 값을 인코딩하는 형식의 단일 값을 반환 합니다.

**참고**

데이터베이스 커서에 대 한 자세한 내용은 [데이터베이스 커서](../management/databasecursor.md) 를 참조 하세요.

::: zone-end

::: zone pivot="azuremonitor"

이 기능은에서 지원 되지 않습니다 Azure Monitor

::: zone-end
