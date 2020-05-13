---
title: max_of ()-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 max_of ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 4b912b1bdc68d7b3071ace8547f0aaf7c679a86a
ms.sourcegitcommit: 733bde4c6bc422c64752af338b29cd55a5af1f88
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/13/2020
ms.locfileid: "83271607"
---
# <a name="max_of"></a>max_of()

계산 된 여러 숫자 식의 최 댓 값을 반환 합니다.

```kusto
max_of(10, 1, -3, 17) == 17
```

**구문**

`max_of``(` *expr_1* `,` *expr_2* expr_1 ...`)`

**인수**

* *expr_i*: 평가할 스칼라 식입니다.

- 모든 인수는 동일한 형식 이어야 합니다.
- 최대 64 개의 인수가 지원 됩니다.

**반환**

모든 인수 식의 최대값입니다.

**예제**

<!-- csl: https://help.kusto.windows.net/Samples  -->
```kusto
print result = max_of(10, 1, -3, 17) 
```

|result|
|---|
|17|
