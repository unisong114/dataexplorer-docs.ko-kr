---
title: isnotempty() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기에서 notempty()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 14111be0fc0247dd151ef7454121e6b90a32ff0d
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81513535"
---
# <a name="isnotempty"></a>isnotempty()

인수가 빈 문자열이 아니거나 null인 경우 반환합니다. `true`

```kusto
isnotempty("") == false
```

**구문**

`isnotempty(`*[값]*`)`

`notempty(`*[값]* `)` -- 의 별칭`isnotempty`