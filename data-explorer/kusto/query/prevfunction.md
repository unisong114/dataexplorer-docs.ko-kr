---
title: prev() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기에서 prev()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 33f6045333826b21ddc0092e2cc7d5e033c12a96
ms.sourcegitcommit: e94be7045d71a0435b4171ca3a7c30455e6dfa57
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81744604"
---
# <a name="prev"></a>prev()

[직렬화된](./windowsfunctions.md#serialized-row-set)행 집합의 현재 행 앞에 있는 일부 오프셋에서 열 값을 반환합니다.

**구문**

`prev(column)`

`prev(column, offset)`

`prev(column, offset, default_value)`

**인수**

* `column`: 값을 얻을 수있는 열입니다.

* `offset`: 행으로 돌아갈 오프셋입니다. 오프셋이 지정되지 않은 경우 기본 오프셋 1이 사용됩니다.

* `default_value`: 값을 취할 이전 행이 없을 때 사용할 기본값입니다. 기본값을 지정하지 않은 경우 null이 사용됩니다.


**예**

```kusto
Table | serialize | extend prevA = prev(A,1)
| extend diff = A - prevA
| where diff > 1

Table | serialize prevA = prev(A,1,10)
| extend diff = A - prevA
| where diff <= 10
```