---
title: current_principal ()-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기에서 current_principal ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 12/10/2019
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 0561ac200105015e6d1c1cce1c16fe5f60fc2ccf
ms.sourcegitcommit: d885c0204212dd83ec73f45fad6184f580af6b7e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82737711"
---
# <a name="current_principal"></a>current_principal()

::: zone pivot="azuredataexplorer"

쿼리를 실행 하는 현재 사용자 이름을 반환 합니다.

**구문**

`current_principal()`

**반환**

현재 보안 주체의 정규화 된 이름 (FQN) `string`입니다.  
문자열은 다음과 같이 구성 됩니다.  
*PrinciplaType*`=`*principalid*`;`*TenantId*

**예제**

```kusto
print fqn=current_principal()
```

|fqn|
|---|
|aaduser = 346e950e-4a62-42bf-96f5-4cf4eac3f11e; 72f988bf-86f1-41af-91ab-2d7cd011db47)|

::: zone-end

::: zone pivot="azuremonitor"

이 기능은에서 지원 되지 않습니다 Azure Monitor

::: zone-end
