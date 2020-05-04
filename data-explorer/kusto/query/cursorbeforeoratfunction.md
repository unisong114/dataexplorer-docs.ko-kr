---
title: cursor_before_or_at ()-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기에서 cursor_before_or_at ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: c053cd307f8cff8ad00eff0a4224ebbea2808c6c
ms.sourcegitcommit: d885c0204212dd83ec73f45fad6184f580af6b7e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82737677"
---
# <a name="cursor_before_or_at"></a>cursor_before_or_at()

::: zone pivot="azuredataexplorer"

테이블의 레코드에 대 한 조건자를 통해 해당 수집 시간을 데이터베이스 커서와 비교할 수 있습니다.

**구문**

`cursor_before_or_at``(` *RHS*`)`

**인수**

* *RHS*: 빈 문자열 리터럴 이거나 올바른 데이터베이스 커서 값입니다.

**반환**

레코드가 데이터베이스 커서 *RHS* `true`( `bool` ) 또는 not (`false`)에 수집 여부를 나타내는 형식의 스칼라 값입니다.

**참고 사항**

데이터베이스 커서에 대 한 자세한 내용은 [데이터베이스 커서](../management/databasecursor.md) 를 참조 하세요.

이 함수는 [IngestionTime 정책을](../management/ingestiontimepolicy.md) 사용 하는 테이블의 레코드에 대해서만 호출할 수 있습니다.

::: zone-end

::: zone pivot="azuremonitor"

이 기능은에서 지원 되지 않습니다 Azure Monitor

::: zone-end
