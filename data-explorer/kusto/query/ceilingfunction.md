---
title: 상한 ()-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 천장 ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: e2a29d28b25d26d582aa49717d5ce5576276f450
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348907"
---
# <a name="ceiling"></a>ceiling()

지정 된 숫자 식 보다 크거나 같은 최소 정수를 계산 합니다.

## <a name="syntax"></a>Syntax

`ceiling(`*.x*`)`

## <a name="arguments"></a>인수

* *x*: 실수입니다.

## <a name="returns"></a>반환

* 지정 된 숫자 식 보다 크거나 같은 최소 정수입니다. 

## <a name="examples"></a>예제

```kusto
print c1 = ceiling(-1.1), c2 = ceiling(0), c3 = ceiling(0.9)
```

|c1|있다면|c3|
|---|---|---|
|-1|0|1|