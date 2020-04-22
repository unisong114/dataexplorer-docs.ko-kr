---
title: cursor_before_or_at() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 cursor_before_or_at()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 4d1752c69a6f3515b94c4050cef8f518ff58a235
ms.sourcegitcommit: 01eb9aaf1df2ebd5002eb7ea7367a9ef85dc4f5d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81765977"
---
# <a name="cursor_before_or_at"></a>cursor_before_or_at()

::: zone pivot="azuredataexplorer"

테이블의 레코드에 대한 조건자로 수집 시간을 데이터베이스 커서와 비교합니다.

**구문**

`cursor_before_or_at``(` *RHS (주)(주)*`)`

**인수**

* *RHS*: 빈 문자열 리터럴 또는 유효한 데이터베이스 커서 값입니다.

**반환**

데이터베이스 커서 `bool` *RHS* () ()에`true``false`레코드가 이전 또는 에서 인지 여부를 나타내는 형식의 스칼라 값입니다.

**참고 사항**

데이터베이스 커서에 대한 자세한 내용은 [데이터베이스 커서를](../management/databasecursor.md) 참조하십시오.

이 함수는 [IngestionTime 정책을](../management/ingestiontimepolicy.md) 사용하도록 설정한 테이블의 레코드에서만 호출할 수 있습니다.

::: zone-end

::: zone pivot="azuremonitor"

Azure 모니터에서는 이 성능이 지원되지 않습니다.

::: zone-end
