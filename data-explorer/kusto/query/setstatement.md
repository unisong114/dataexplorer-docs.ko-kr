---
title: Set 문-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 Set 문을 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 028bfb5a2d0ddf25f65cd16bca2c498d9dcb7059
ms.sourcegitcommit: d885c0204212dd83ec73f45fad6184f580af6b7e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82737864"
---
# <a name="set-statement"></a>Set 문

::: zone pivot="azuredataexplorer"

`set` 문은 쿼리 기간에 대 한 쿼리 옵션을 설정 하는 데 사용 됩니다.
쿼리 옵션은 쿼리가 실행되고 결과가 반환되는 방법을 제어합니다. 부울 플래그 (기본적으로 해제 됨) 이거나 정수 값을 가질 수 있습니다. 쿼리는 0개 이상의 set 문을 포함할 수 있습니다. Set 문은 프로그램 순서로 추적 하는 테이블 형식 식 문에만 영향을 줍니다.

* 쿼리 옵션은 `ClientRequestProperties` 개체에서 설정 하 여 프로그래밍 방식으로 사용 하도록 설정할 수도 있습니다. [여기](../api/netfx/request-properties.md)를 참조 하세요.
  
* 쿼리 옵션은 공식적으로 Kusto 언어에 포함 되지 않으며, 주요 언어 변경으로 간주 하지 않고도 수정할 수 있습니다.

**구문**

`set`*OptionName* [`=` *OptionValue*]

**예제**

```kusto
set querytrace;
Events | take 100
```

::: zone-end

::: zone pivot="azuremonitor"

이 기능은에서 지원 되지 않습니다 Azure Monitor

::: zone-end
