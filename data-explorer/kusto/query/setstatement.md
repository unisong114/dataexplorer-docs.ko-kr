---
title: 문 설정 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기에서 집합 문을 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 8cb9c1d72f1b2e8e4bfbbd28d67c04295c9ccf5b
ms.sourcegitcommit: 01eb9aaf1df2ebd5002eb7ea7367a9ef85dc4f5d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81765580"
---
# <a name="set-statement"></a>Set 문

::: zone pivot="azuredataexplorer"

문은 `set` 쿼리 기간 동안 쿼리 옵션을 설정하는 데 사용됩니다.
쿼리 옵션은 쿼리가 실행되고 결과가 반환되는 방법을 제어합니다. 부울 플래그(기본적으로 꺼져 있음)이거나 정수 값을 가질 수 있습니다. 쿼리는 0개 이상의 set 문을 포함할 수 있습니다. set 문은 프로그램 순서대로 추적되는 테이블 형식 표현문만 영향을 미칩니다.

* 쿼리 옵션은 `ClientRequestProperties` 개체에 설정하여 프로그래밍 방식으로 사용할 수도 있습니다. [여기를](../api/netfx/request-properties.md)참조하십시오.
  
* 쿼리 옵션은 공식적으로 Kusto 언어의 일부가 아니며 주요 언어 변경으로 간주되지 않고 수정할 수 있습니다.

**구문**

`set`*옵션* 이름`=` [ *옵션 값*]

**예제**

```kusto
set querytrace;
Events | take 100
```

::: zone-end

::: zone pivot="azuremonitor"

Azure 모니터에서는 이 성능이 지원되지 않습니다.

::: zone-end
