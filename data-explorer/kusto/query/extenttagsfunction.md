---
title: extent_tags() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 extent_tags()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: d8af7e51c5e2efb16763541db05e9ccc7e2cb95f
ms.sourcegitcommit: 01eb9aaf1df2ebd5002eb7ea7367a9ef85dc4f5d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81765417"
---
# <a name="extent_tags"></a>extent_tags()

::: zone pivot="azuredataexplorer"

현재 레코드가 있는 데이터 샤드("익스텐츠")의 [태그가](../management/extents-overview.md#extent-tagging) 있는 동적 배열을 반환합니다. 

이 함수를 데이터 샤드에 연결되지 않은 계산된 데이터에 적용하면 빈 값이 반환됩니다.

**구문**

`extent_tags()`

**반환**

현재 레코드의 `dynamic` 익스텐트 태그 또는 빈 값을 보유하는 배열인 형식의 값입니다.

**예**

다음 예제에서는 열에 `ActivityId`대한 특정 값으로 한 시간 전에 레코드가 있는 모든 데이터 샤드의 태그를 목록으로 표시하는 방법을 보여 주었습니다. 일부 쿼리 `where` 연산자(여기서는 연산자이지만 이 것도 `extend` `project`마찬가지입니다.) 레코드를 호스팅하는 데이터 샤드에 대한 정보를 보존합니다.

```kusto
T
| where Timestamp > ago(1h)
| where ActivityId == 'dd0595d4-183e-494e-b88e-54c52fe90e5a'
| extend tags = extent_tags()
| summarize by tostring(tags)
```

다음 예제에서는 태그(및 `MyTag` 잠재적으로 다른 태그)로 태그가 지정되지만 태그로 `drop-by:MyOtherTag`태그가 지정되지 않은 익스텐트에 저장되는 마지막 1시간의 모든 레코드 수를 가져오는 방법을 보여 주며.

```kusto
T
| where Timestamp > ago(1h)
| extend Tags = extent_tags()
| where Tags has_cs 'MyTag' and Tags !has_cs 'drop-by:MyOtherTag'
| count
```

::: zone-end

::: zone pivot="azuremonitor"

Azure 모니터에서는 이 성능이 지원되지 않습니다.

::: zone-end
