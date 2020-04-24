---
title: current_principal_details() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 current_principal_details()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 11/08/2019
ms.openlocfilehash: 5418647c811b034bb5790dfff3fd17f500c52db0
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81516782"
---
# <a name="current_principal_details"></a>current_principal_details()

쿼리를 실행하는 보안 주체에 대한 세부 정보를 반환합니다.

**구문**

`current_principal_details()`

**반환**

현재 보안 주체의 `dynamic`세부 정보입니다.

**예제**

```kusto
print d=current_principal_details()
```

|d|
|---|
|{<br>  "사용자 보안 주체user@fabrikam.com이름": " "<br>  "IDProvider":https://sts.windows.net" "<br>  "기관": "72f988bf-86f1-41af-91ab-2d7cd011db47",<br>  "Mfa": "트루",<br>  "유형": "AadUser",<br>  "디스플레이 이름": "제임스 스미스 user@fabrikam.com(upn: )",<br>  "ObjectId": "346e950e-4a62-42bf-96f5-4cf4eac3f11e",<br>  "FQN": null,<br>  "참고 사항": null<br>}|
