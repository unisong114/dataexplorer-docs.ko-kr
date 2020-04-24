---
title: min_of() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 min_of()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 06a8f7ce6bcef8f3c15c4ea3d4c997b4e4540bf7
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81512396"
---
# <a name="min_of"></a>min_of()

평가된 여러 숫자 식의 최소값을 반환합니다.

```kusto
min_of(10, 1, -3, 17) == -3
```

**구문**

`min_of``(` *expr_1* expr_1`,` *expr_2* ...`)`

**인수**

* *expr_i*: 평가할 스칼라 식입니다.

- 모든 인수는 동일한 형식이어야 합니다.
- 최대 64개의 인수가 지원됩니다.

**반환**

모든 인수 식의 최소값입니다.

**예제**

```kusto
print result=min_of(10, 1, -3, 17) 
```

|result|
|---|
|-3|