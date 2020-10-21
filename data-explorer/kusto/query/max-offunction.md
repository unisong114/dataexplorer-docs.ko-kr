---
title: max_of ()-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 max_of ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: f2a9cbb64ce1bbbc7d58b66c260d7968d8a60748
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92248949"
---
# <a name="max_of"></a>max_of()

계산 된 여러 숫자 식의 최 댓 값을 반환 합니다.

```kusto
max_of(10, 1, -3, 17) == 17
```

## <a name="syntax"></a>구문

`max_of``(` *expr_1* `,` *expr_2* expr_1 ...`)`

## <a name="arguments"></a>인수

* *expr_i*: 평가할 스칼라 식입니다.

- 모든 인수는 동일한 형식 이어야 합니다.
- 최대 64 개의 인수가 지원 됩니다.

## <a name="returns"></a>반환

모든 인수 식의 최대값입니다.

## <a name="example"></a>예제

<!-- csl: https://help.kusto.windows.net/Samples  -->
```kusto
print result = max_of(10, 1, -3, 17) 
```

|result|
|---|
|17|
