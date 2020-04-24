---
title: 다음() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기에서 다음()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: f45e88942fdf9eb23451e1391866f57ca5f0e21a
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81512124"
---
# <a name="next"></a>next()

[직렬화된](./windowsfunctions.md#serialized-row-set)행 집합의 현재 행 다음에 일부 오프셋에서 열 값을 반환합니다.

**구문**

`next(column)`

`next(column, offset)`

`next(column, offset, default_value)`

**인수**

* `column`: 값을 얻을 수있는 열입니다.

* `offset`: 행에서 진행할 오프셋입니다. 오프셋이 지정되지 않은 경우 기본 오프셋 1이 사용됩니다.

* `default_value`: 값을 취할 다음 행이 없을 때 사용할 기본값입니다. 기본값을 지정하지 않은 경우 null이 사용됩니다.


**예**
```kusto
Table | serialize | extend nextA = next(A,1)
| extend diff = A - nextA
| where diff > 1

Table | serialize nextA = next(A,1,10)
| extend diff = A - nextA
| where diff <= 10
```