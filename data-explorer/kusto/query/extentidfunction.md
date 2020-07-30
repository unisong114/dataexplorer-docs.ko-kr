---
title: extent_id ()-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기에서 extent_id ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 22f1b36b851c6e629abd2524feb4c40c74bbb1fa
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348142"
---
# <a name="extent_id"></a>extent_id()

::: zone pivot="azuredataexplorer"

현재 레코드가 있는 데이터 분할 ("익스텐트")을 식별 하는 고유 식별자를 반환 합니다.

데이터 분할 된 데이터에 연결 되지 않은 계산 된 데이터에이 함수를 적용 하면 빈 guid (모두 0)가 반환 됩니다.

## <a name="syntax"></a>Syntax

`extent_id()`

## <a name="returns"></a>반환

`guid`현재 레코드의 데이터 분할 된 영역 또는 빈 guid (모두 0)를 식별 하는 형식의 값입니다.

## <a name="example"></a>예제

다음 예에서는 열에 대 한 특정 값을 사용 하 여 1 시간 전에 레코드를 포함 하는 모든 데이터 분할 목록을 가져오는 방법을 보여 줍니다 `ActivityId` . 일부 쿼리 연산자 (여기서 `where` 연산자 및 `extend` 및 `project` )는 레코드를 호스트 하는 데이터 분할 된 데이터에 대 한 정보를 유지 함을 보여 줍니다.

```kusto
T
| where Timestamp > ago(1h)
| where ActivityId == 'dd0595d4-183e-494e-b88e-54c52fe90e5a'
| extend eid=extent_id()
| summarize by eid
```

::: zone-end

::: zone pivot="azuremonitor"

이 기능은에서 지원 되지 않습니다 Azure Monitor

::: zone-end
