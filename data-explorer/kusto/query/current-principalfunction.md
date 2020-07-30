---
title: current_principal ()-Azure 데이터 탐색기
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
ms.openlocfilehash: 7fa1ad900eb91390436e88c44ad9fd7394ad087d
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348652"
---
# <a name="current_principal"></a>current_principal()

::: zone pivot="azuredataexplorer"

쿼리를 실행 하는 현재 사용자 이름을 반환 합니다.

## <a name="syntax"></a>구문

`current_principal()`

## <a name="returns"></a>반환

현재 보안 주체의 FQN (정규화 된 이름) `string` 입니다.  
문자열 형식은 다음과 같습니다.  
*PrinciplaType* `=` *Principalid* `;` *TenantId*

## <a name="example"></a>예제

<!-- csl: https://help.kusto.windows.net/Samples -->
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
