---
title: 별칭 문 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 별칭 문을 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: c6c689ab6daacebe1cd20742b199c8b9cc299245
ms.sourcegitcommit: 01eb9aaf1df2ebd5002eb7ea7367a9ef85dc4f5d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81766082"
---
# <a name="alias-statement"></a>Alias 문

::: zone pivot="azuredataexplorer"

별칭 문을 사용하면 동일한 쿼리의 나중에 사용할 수 있는 데이터베이스에 대한 별칭을 정의할 수 있습니다.

이 기능은 적은 클러스터에서 또는 한 클러스터에서만 작업하는 동안 여러 클러스터로 작업하는 경우에 유용합니다.
*별칭은 클러스터 이름* 및 데이터베이스 *이름이* 기존 및 유효한 엔터티여야 하는 다음 구문에 따라 정의되어야 합니다.

**구문**

`alias`데이터베이스[*'DatabaseAliasName'*] `=` 클러스터("https://클러스터*이름*.kusto.windows.net:443").데이터베이스*이름")*

`alias`데이터베이스 *데이터베이스AliasName* `=` 클러스터("https://클러스터*이름*.kusto.windows.net:443").데이터베이스("데이터베이스*이름")*

* *'DatabaseAliasName'은* en 축 이름 또는 새 이름일 수 있습니다.
* 매핑된 클러스터 uri 및 매핑된 데이터베이스 이름은 큰따옴표(") 또는 단일 따옴표(') 안에 나타나야 합니다.

**예**

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

Azure 모니터에서는 이 성능이 지원되지 않습니다.

::: zone-end
