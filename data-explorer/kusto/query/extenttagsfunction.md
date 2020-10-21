---
title: extent_tags ()-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기에서 extent_tags ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: da705d558a09bdcc52bf07fc807e53fdccb9396c
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92249090"
---
# <a name="extent_tags"></a>extent_tags()

::: zone pivot="azuredataexplorer"

현재 레코드가 있는 데이터 분할 ("익스텐트")의 [태그가](../management/extents-overview.md#extent-tagging) 있는 동적 배열을 반환 합니다. 

데이터 분할 된 데이터에 연결 되지 않은 계산 된 데이터에이 함수를 적용 하면 빈 값이 반환 됩니다.

## <a name="syntax"></a>구문

`extent_tags()`

## <a name="returns"></a>반환

`dynamic`현재 레코드의 범위 태그를 포함 하는 배열 또는 빈 값 형식의 값입니다.

## <a name="examples"></a>예

다음 예에서는 1 시간 전에 레코드를 포함 하는 모든 데이터 분할의 태그를 열에 대 한 특정 값으로 나열 하는 방법을 보여 줍니다 `ActivityId` . 일부 쿼리 연산자 (여기서는 `where` 연산자 이지만 및의 경우에도 해당 됨 `extend` )는 레코드를 호스트 하는 데이터 분할 된 `project` 데이터에 대 한 정보를 유지 하는 것을 보여 줍니다.

```kusto
T
| where Timestamp > ago(1h)
| where ActivityId == 'dd0595d4-183e-494e-b88e-54c52fe90e5a'
| extend tags = extent_tags()
| summarize by tostring(tags)
```

다음 예제에서는 마지막 시간부터 모든 레코드의 수를 가져오는 방법을 보여 줍니다 .이는 태그 `MyTag` (및 잠재적으로 다른 태그)로 태그가 지정 되었지만 태그로 태그가 지정 되지 않은 익스텐트에 저장 됩니다 `drop-by:MyOtherTag` .

```kusto
T
| where Timestamp > ago(1h)
| extend Tags = extent_tags()
| where Tags has_cs 'MyTag' and Tags !has_cs 'drop-by:MyOtherTag'
| count
```

::: zone-end

::: zone pivot="azuremonitor"

이 기능은에서 지원 되지 않습니다 Azure Monitor

::: zone-end
