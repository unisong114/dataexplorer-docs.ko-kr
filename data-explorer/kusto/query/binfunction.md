---
title: bin ()-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 bin ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 9bafeee9cec5ac81034b879f054e445d8b118dcf
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92243405"
---
# <a name="bin"></a>bin()

값을 지정된 bin 크기의 아래쪽 정수 배로 반올림합니다. 

와 함께 자주 사용 [`summarize by ...`](./summarizeoperator.md) 됩니다.
분산된 값 집합이 있는 경우 특정 값의 더 작은 집합으로 그룹화됩니다.

Null 값, null bin 크기 또는 음수 bin 크기를 반환 하면 null이 반환 됩니다. 

함수에 대 한 별칭 `floor()` 입니다.

## <a name="syntax"></a>구문

`bin(`*값* `,` *roundTo*`)`

## <a name="arguments"></a>인수

* *value*: 숫자, 날짜 또는 timespan입니다. 
* *roundTo*: "bin size" *값*을 나누는 숫자 또는 timespan입니다. 

## <a name="returns"></a>반환

*값*보다 작은 *roundTo*의 가장 가까운 배수입니다.  
 
```kusto
(toint((value/roundTo))) * roundTo`
```

## <a name="examples"></a>예

식 | 결과
---|---
`bin(4.5, 1)` | `4.0`
`bin(time(16d), 7d)` | `14d`
`bin(datetime(1970-05-11 13:45:07), 1d)`|  `datetime(1970-05-11)`


다음 식은 버킷 크기 1초를 사용하여 기간의 히스토그램을 계산합니다.

```kusto
T | summarize Hits=count() by bin(Duration, 1s)
```
