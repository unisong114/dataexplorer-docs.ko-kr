---
title: max_of() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 max_of()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 68188ccd5eb814a22be166b8847d80193172813f
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81512498"
---
# <a name="max_of"></a>max_of()

평가된 여러 숫자 식의 최대값을 반환합니다.

```kusto
max_of(10, 1, -3, 17) == 17
```

**구문**

`max_of``(` *expr_1* expr_1`,` *expr_2* ...`)`

**인수**

* *expr_i*: 평가할 스칼라 식입니다.

- 모든 인수는 동일한 형식이어야 합니다.
- 최대 64개의 인수가 지원됩니다.

**반환**

모든 인수 식의 최대값입니다.

**예제**

```kusto
print result = max_of(10, 1, -3, 17) 
```

|result|
|---|
|17|