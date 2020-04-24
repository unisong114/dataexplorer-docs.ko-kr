---
title: bitset_count_ones() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 bitset_count_ones()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/22/2020
ms.openlocfilehash: c23e95ee9f00a0ca173d68d3591ad604b31dfac2
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81517394"
---
# <a name="bitset_count_ones"></a>bitset_count_ones()

숫자의 이진 표현에서 세트 비트 수를 반환합니다.

```kusto
bitset_count_ones(42)
```

**구문**

`bitset_count_ones(`*num1*'')'

**인수**

* *num1*: 긴 또는 정수 번호입니다.

**반환**

숫자의 이진 표현에서 세트 비트 수를 반환합니다.

**예제**

```kusto
// 42 = 32+8+2 : b'00101010' == 3 bits set
print ones = bitset_count_ones(42) 
```

|사람|
|---|
|3|