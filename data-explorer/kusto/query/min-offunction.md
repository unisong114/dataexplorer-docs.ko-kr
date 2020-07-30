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
ms.openlocfilehash: ed8d14a4e793f253342c1b52269678874c96660f
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87346765"
---
# <a name="min_of"></a>min_of()

계산 된 여러 숫자 식의 최 솟 값을 반환 합니다.

```kusto
min_of(10, 1, -3, 17) == -3
```

## <a name="syntax"></a>Syntax

`min_of``(` *expr_1* `,` *expr_2* expr_1 ...`)`

## <a name="arguments"></a>인수

* *expr_i*: 평가할 스칼라 식입니다.

- 모든 인수는 동일한 형식 이어야 합니다.
- 최대 64 개의 인수가 지원 됩니다.

## <a name="returns"></a>반환

모든 인수 식의 최소값입니다.

## <a name="example"></a>예제

<!-- csl: https://help.kusto.windows.net/Samples  -->
```kusto
print result=min_of(10, 1, -3, 17) 
```

|result|
|---|
|-3|
