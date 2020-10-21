---
title: next ()-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 다음 ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: ca9361c0a43a2881f7448312e4f8a5129426e55a
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92248739"
---
# <a name="next"></a>next()

[Serialize 된 행 집합](./windowsfunctions.md#serialized-row-set)에서 현재 행 다음의 특정 오프셋에서 행의 열 값을 반환 합니다.

## <a name="syntax"></a>구문

`next(column)`

`next(column, offset)`

`next(column, offset, default_value)`

## <a name="arguments"></a>인수

* `column`: 값을 가져올 열입니다.

* `offset`: 행의 앞으로 이동 하는 오프셋입니다. 오프셋이 지정 되지 않은 경우 기본 오프셋 1이 사용 됩니다.

* `default_value`: 값을 가져올 다음 행이 없는 경우 사용 되는 기본값입니다. 기본값을 지정 하지 않으면 null이 사용 됩니다.


## <a name="examples"></a>예
```kusto
Table | serialize | extend nextA = next(A,1)
| extend diff = A - nextA
| where diff > 1

Table | serialize nextA = next(A,1,10)
| extend diff = A - nextA
| where diff <= 10
```