---
title: isnan ()-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 isnan ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: f73effae8d91524f46548d57288a23d79cffd0a5
ms.sourcegitcommit: 4e95f5beb060b5d29c1d7bb8683695fe73c9f7ea
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/23/2020
ms.locfileid: "91103272"
---
# <a name="isnan"></a>isnan()

입력이 NaN (숫자가 아님) 값 인지 여부를 반환 합니다.  

## <a name="syntax"></a>구문

`isnan(`*.x*`)`

## <a name="arguments"></a>인수

* *x*: 실수입니다.

## <a name="returns"></a>반환

X가 NaN 이면 0이 아닌 값 (true)입니다. 그렇지 않으면 0 (false)입니다.

## <a name="see-also"></a>참고 항목

* 값이 null 인지 확인 하려면 [isnull ()](isnullfunction.md)을 참조 하세요.
* 값이 유한 인지 확인 하려면 [isfinite ()](isfinitefunction.md)을 참조 하세요.
* 값이 무한 인지 확인 하려면 [isinf ()](isinffunction.md)를 참조 하세요.

## <a name="example"></a>예제

```kusto
range x from -1 to 1 step 1
| extend y = (-1*x) 
| extend div = 1.0*x/y
| extend isnan=isnan(div)
```

|x|y|div|isnan|
|---|---|---|---|
|-1|1|-1|0|
|0|0|NaN|1|
|1|-1|-1|0|