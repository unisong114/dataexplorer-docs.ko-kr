---
title: rand() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기에서 rand()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: d3638d4b979b7318f58efec0bed0da4c31896a9d
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81510645"
---
# <a name="rand"></a>rand()

난수를 반환합니다.

```kusto
rand()
rand(1000)
```

**구문**

* `rand()`- 범위 [0.0, 1.0)에서 균일 한 분포를 가진 형식의 `real` 값을 반환합니다.
* `rand(`*N* `)` - {0.0, 1.0, ..., N - `real` *1}세트에서* 균일한 분포로 선택한 형식의 값을 반환합니다.