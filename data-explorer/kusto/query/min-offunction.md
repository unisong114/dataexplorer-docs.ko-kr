---
title: min_of ()-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 min_of ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 0762b1416df32279b9801c47f129a6966772a7e2
ms.sourcegitcommit: 733bde4c6bc422c64752af338b29cd55a5af1f88
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/13/2020
ms.locfileid: "83271369"
---
# <a name="min_of"></a>min_of()

계산 된 여러 숫자 식의 최 솟 값을 반환 합니다.

```kusto
min_of(10, 1, -3, 17) == -3
```

**구문**

`min_of``(` *expr_1* `,` *expr_2* expr_1 ...`)`

**인수**

* *expr_i*: 평가할 스칼라 식입니다.

- 모든 인수는 동일한 형식 이어야 합니다.
- 최대 64 개의 인수가 지원 됩니다.

**반환**

모든 인수 식의 최소값입니다.

**예제**

<!-- csl: https://help.kusto.windows.net/Samples  -->
```kusto
print result=min_of(10, 1, -3, 17) 
```

|result|
|---|
|-3|
