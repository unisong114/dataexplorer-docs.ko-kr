---
title: not() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기에서 not()를 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 539e409a9e922afc390b097c863146b7fc30d7b3
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81512039"
---
# <a name="not"></a>not()

`bool` 인수의 값을 반대로 합니다.

```kusto
not(false) == true
```

**구문**

`not(`*Expr*`)`

**인수**

* *expr*: `bool` 반전할 식입니다.

**반환**

해당 인수의 역반전된 논리 값을 반환합니다. `bool`