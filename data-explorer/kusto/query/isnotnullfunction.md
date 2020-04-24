---
title: isnotnull() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기에서 isnotnull()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 8be57f2f7484081ac316a8af6fea252a60f895a4
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81513450"
---
# <a name="isnotnull"></a>이스노널()

인수가 null이 아닌 경우 반환합니다. `true`

**구문**

`isnotnull(`*[값]*`)`

`notnull(`*[값]* `)` - 별칭`isnotnull`

**예제**

```kusto
T | where isnotnull(PossiblyNull) | count
```

참고로 이 효과를 달성하는 다른 방법이 있습니다.

```kusto
T | summarize count(PossiblyNull)
```