---
title: 상한 ()-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 천장 ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: b307121e102229edbe62c4d4dd7f910ea2ce8756
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92249327"
---
# <a name="ceiling"></a>ceiling()

지정 된 숫자 식 보다 크거나 같은 최소 정수를 계산 합니다.

## <a name="syntax"></a>구문

`ceiling(`*.x*`)`

## <a name="arguments"></a>인수

* *x*: 실수입니다.

## <a name="returns"></a>반환

* 지정 된 숫자 식 보다 크거나 같은 최소 정수입니다. 

## <a name="examples"></a>예

```kusto
print c1 = ceiling(-1.1), c2 = ceiling(0), c3 = ceiling(0.9)
```

|c1|c2|c3|
|---|---|---|
|-1|0|1|