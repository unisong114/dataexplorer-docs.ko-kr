---
title: isfinite ()-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 isfinite ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: b428d43afd7984bbcf19351da702517a3a1244a9
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92242316"
---
# <a name="isfinite"></a>isfinite()

입력이 유한 값 인지 여부를 반환 합니다 (무한도 아니면 NaN).

## <a name="syntax"></a>구문

`isfinite(`*.x*`)`

## <a name="arguments"></a>인수

* *x*: 실수입니다.

## <a name="returns"></a>반환

X가 유한 하면 0이 아닌 값 (true)입니다. 그렇지 않으면 0 (false)입니다.

## <a name="see-also"></a>참조

* 값이 null 인지 확인 하려면 [isnull ()](isnullfunction.md)을 참조 하세요.
* 값이 무한 인지 확인 하려면 [isinf ()](isinffunction.md)를 참조 하세요.
* 값이 NaN (숫자가 아님) 인지 확인 하려면 [isnan ()](isnanfunction.md)을 참조 하세요.

## <a name="example"></a>예제

```kusto
range x from -1 to 1 step 1
| extend y = 0.0
| extend div = 1.0*x/y
| extend isfinite=isfinite(div)
```

|x|y|div|isfinite|
|---|---|---|---|
|-1|0|-∞|0|
|0|0|NaN|0|
|1|0|∞|0|