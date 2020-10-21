---
title: Alias 문-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 별칭 문에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 822c8eccf50dc30fd3f56f4402c10a9fafb34084
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92248284"
---
# <a name="alias-statement"></a>Alias 문

::: zone pivot="azuredataexplorer"

Alias 문을 사용 하면 나중에 동일한 쿼리에서 사용할 수 있는 데이터베이스에 대 한 별칭을 정의할 수 있습니다.

이는 여러 클러스터로 작업 하는 경우에 유용 하지만 더 작은 클러스터에서 작업 하는 것 처럼 표시 하려는 경우에 유용 합니다.
다음 구문에 따라 별칭을 정의 해야 합니다. 여기서 *clustername* 및 *databasename* 은 기존 엔터티와 유효한 엔터티입니다.

## <a name="syntax"></a>구문

`alias` 데이터베이스 [*' DatabaseAliasName '*] `=` cluster ("https://*clustername*. kusto. net.tcp: 443") .database ("*databasename*")

`alias` database *DatabaseAliasName* `=` cluster ("https://*clustername*. kusto. net: 443") .database ("*databasename*")

* *' DatabaseAliasName '* 는 기존 이름 이거나 새 이름일 수 있습니다.
* 매핑된 클러스터 uri와 매핑된 데이터베이스 이름은 큰따옴표 (") 또는 작은따옴표 (') 안에 표시 되어야 합니다.

## <a name="examples"></a>예

```kusto
alias database["wiki"] = cluster("https://somecluster.kusto.windows.net:443").database("somedatabase");
database("wiki").PageViews | count 
```

```kusto
alias database Logs = cluster("https://othercluster.kusto.windows.net:443").database("otherdatabase");
database("Logs").Traces | count 
```

::: zone-end

::: zone pivot="azuremonitor"

이 기능은에서 지원 되지 않습니다 Azure Monitor

::: zone-end
