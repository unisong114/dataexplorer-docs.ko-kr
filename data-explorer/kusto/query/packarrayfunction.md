---
title: pack_array ()-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 pack_array ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: ad6403a70a8f71eee05bc56789cae2d6287a6605
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87346527"
---
# <a name="pack_array"></a>pack_array()

모든 입력 값을 동적 배열로 압축 합니다.

## <a name="syntax"></a>Syntax

`pack_array(`*Expr1* `[` , ` *Expr2*]` ) '

## <a name="arguments"></a>인수

* *Expr1 ... N*: 동적 배열로 압축할 입력 식입니다.

## <a name="returns"></a>반환

Expr1, Expr2, ..., ExprN의 값을 포함 하는 동적 배열입니다.

## <a name="example"></a>예제

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
range x from 1 to 3 step 1
| extend y = x * 2
| extend z = y * 2
| project pack_array(x,y,z)
```

|열1|
|---|
|[1, 2, 4]|
|[2, 4, 8]|
|[3, 6, 12]|

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
range x from 1 to 3 step 1
| extend y = tostring(x * 2)
| extend z = (x * 2) * 1s
| project pack_array(x,y,z)
```

|열1|
|---|
|[1, "2", "00:00:02"]|
|[2, "4", "00:00:04"]|
|[3, "6", "00:00:06"]|
