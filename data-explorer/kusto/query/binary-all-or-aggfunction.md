---
title: binary_all_or() (집계 기능) - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 binary_all_or() (집계 함수)에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
ms.openlocfilehash: 5de240f606e53b26996ebfe11073170758233e2c
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81517717"
---
# <a name="binary_all_or-aggregation-function"></a>binary_all_or() (집계 함수)

요약 그룹당 이진 `OR` 연산을 사용하여 값을 누적합니다(또는 그룹화 없이 요약이 수행되는 경우 전체적으로).

* 요약 내부 집계의 컨텍스트에서만 사용할 수 [있습니다.](summarizeoperator.md)

**구문**

`binary_all_or(` *예시* 요약`)`

**인수**

* *예시*: 긴 수.

**반환**

요약 그룹별 레코드(또는 그룹화 없이 요약이 수행된 경우) 동안 이진 `OR` 연산을 사용하여 집계된 값을 반환합니다.

**예제**

바이너리 `OR` 연산을 사용하여 '카페 음식' 생산:

```kusto
datatable(num:long)
[
  0x88888008,
  0x42000000,
  0x00767000,
  0x00000005, 
]
| summarize result = toupper(tohex(binary_all_or(num)))
```

|result|
|---|
|카페프00D|