---
title: bin() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기에서 bin()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 3fb827c71fa63fde031a91bc9aec7f0ed108fd5c
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81517428"
---
# <a name="bin"></a>bin()

값을 지정된 bin 크기의 아래쪽 정수 배로 반올림합니다. 

와 [`summarize by ...`](./summarizeoperator.md)함께 자주 사용됩니다.
분산된 값 집합이 있는 경우 특정 값의 더 작은 집합으로 그룹화됩니다.

Null 값, null bin 크기 또는 음수 bin 크기는 null이 됩니다. 

기능할 `floor()` 별칭입니다.

**구문**

`bin(`*값*`,`*둥근To*`)`

**인수**

* *값*: 숫자, 날짜 또는 시간 범위입니다. 
* *roundTo*: "빈 크기". *값*을 나누는 숫자, 날짜 또는 시간 범위입니다. 

**반환**

*값*보다 작은 *roundTo*의 가장 가까운 배수입니다.  
 
```kusto
(toint((value/roundTo))) * roundTo`
```

**예**

식 | 결과
---|---
`bin(4.5, 1)` | `4.0`
`bin(time(16d), 7d)` | `14d`
`bin(datetime(1970-05-11 13:45:07), 1d)`|  `datetime(1970-05-11)`


다음 식은 버킷 크기 1초를 사용하여 기간의 히스토그램을 계산합니다.

```kusto
T | summarize Hits=count() by bin(Duration, 1s)
```