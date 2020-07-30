---
title: prev ()-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 prev ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: fb781834d77aee678103a14714721ff0d46f7b3a
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87346102"
---
# <a name="prev"></a>prev()

지정 된 행에 있는 특정 열의 값을 반환 합니다.
지정 된 행이 [직렬화 된 행 집합](./windowsfunctions.md#serialized-row-set)의 현재 행에서 지정 된 오프셋에 있습니다.

## <a name="syntax"></a>Syntax

여러 가지 가능성이 있습니다.

* `prev(column)`

* `prev(column, offset)`

* `prev(column, offset, default_value)`

## <a name="arguments"></a>인수

* `column`: 값을 가져올 열입니다.

* `offset`: 행으로 다시 이동 하는 오프셋입니다. 오프셋을 지정 하지 않으면 기본 오프셋 1이 사용 됩니다.

* `default_value`: 값을 가져올 이전 행이 없을 때 사용 되는 기본값입니다. 기본값을 지정 하지 않으면 null이 사용 됩니다.

## <a name="examples"></a>예제

```kusto
Table | serialize | extend prevA = prev(A,1)
| extend diff = A - prevA
| where diff > 1

Table | serialize prevA = prev(A,1,10)
| extend diff = A - prevA
| where diff <= 10
```
