---
title: bin() - Azure Data Explorer | Microsoft Docs
description: 이 문서에서는 Azure Data Explorer의 bin()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.localizationpriority: high
ms.openlocfilehash: 6fc2e55b43e7c7c2dc2bb537730f8f627e3e4a66
ms.sourcegitcommit: f49e581d9156e57459bc69c94838d886c166449e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2020
ms.locfileid: "95513117"
---
# <a name="bin"></a>bin()

값을 지정된 bin 크기의 아래쪽 정수 배로 반올림합니다. 

[`summarize by ...`](./summarizeoperator.md)와 함께 자주 사용됩니다.
분산된 값 집합이 있는 경우 특정 값의 더 작은 집합으로 그룹화됩니다.

Null 값, null bin 크기 또는 음수 bin 크기는 null이 됩니다. 

`floor()` 함수에 대한 별칭입니다.

## <a name="syntax"></a>Syntax

`bin(`*value*`,`*roundTo*`)`

## <a name="arguments"></a>인수

* *value*: 숫자, 날짜 또는 시간 범위입니다. 
* *roundTo*: "bin 크기"입니다. *값* 을 나누는 숫자 또는 시간 범위입니다. 

## <a name="returns"></a>반환

*값* 보다 작은 *roundTo* 의 가장 가까운 배수입니다.  
 
```kusto
(toint((value/roundTo))) * roundTo`
```

## <a name="examples"></a>예제

식 | 결과
---|---
`bin(4.5, 1)` | `4.0`
`bin(time(16d), 7d)` | `14d`
`bin(datetime(1970-05-11 13:45:07), 1d)`|  `datetime(1970-05-11)`


다음 식은 버킷 크기 1초를 사용하여 기간의 히스토그램을 계산합니다.

```kusto
T | summarize Hits=count() by bin(Duration, 1s)
```
