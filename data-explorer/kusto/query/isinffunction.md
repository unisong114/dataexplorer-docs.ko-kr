---
title: isinf ()-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 isinf ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 71a37d7a1bd700b5f929c009197a382315099e08
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347241"
---
# <a name="isinf"></a>isinf()

입력이 무한 (양수 또는 음수) 값 인지 여부를 반환 합니다.  

## <a name="syntax"></a>Syntax

`isinf(`*.x*`)`

## <a name="arguments"></a>인수

* *x*: 실수입니다.

## <a name="returns"></a>반환

X가 양수인 경우 0이 아닌 값 (true)이 고, 그렇지 않으면입니다. 그렇지 않으면 0 (false)입니다.

**참고 항목**

* 값이 null 인지 확인 하려면 [isnull ()](isnullfunction.md)을 참조 하세요.
* 값이 유한 인지 확인 하려면 [isfinite ()](isfinitefunction.md)을 참조 하세요.
* 값이 NaN (숫자가 아님) 인지 확인 하려면 [isnan ()](isnanfunction.md)을 참조 하세요.

## <a name="example"></a>예제

```kusto
range x from -1 to 1 step 1
| extend y = 0.0
| extend div = 1.0*x/y
| extend isinf=isinf(div)
```

|x|y|div|isinf|
|---|---|---|---|
|-1|0|-∞|1|
|0|0|NaN|0|
|1|0|∞|1|
