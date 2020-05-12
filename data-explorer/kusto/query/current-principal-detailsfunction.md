---
title: current_principal_details ()-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 current_principal_details ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 11/08/2019
ms.openlocfilehash: f71770d2cc9d44987731a247fa8eb945ed323391
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83227516"
---
# <a name="current_principal_details"></a>current_principal_details()

쿼리를 실행 하는 보안 주체의 세부 정보를 반환 합니다.

**구문**

`current_principal_details()`

**반환**

로 서의 현재 보안 주체에 대 한 세부 정보 `dynamic` 입니다.

**예제**

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print d=current_principal_details()
```

|d|
|---|
|{<br>  "UserPrincipalName": " user@fabrikam.com ",<br>  "IdentityProvider": " https://sts.windows.net ",<br>  "Authority": "72f988bf-86f1-41af-91ab-2d7cd011db47)",<br>  "Mfa": "True",<br>  "Type": "AadUser",<br>  "DisplayName": "James Smith (upn: user@fabrikam.com )",<br>  "ObjectId": "346e950e-4a62-42bf-96f5-4cf4eac3f11e",<br>  "FQN": null,<br>  "참고": null<br>}|
