---
title: bitset_count_ones ()-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 bitset_count_ones ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/22/2020
ms.openlocfilehash: f8abb1683a2f15f012e9a9271681688c19901af0
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83227601"
---
# <a name="bitset_count_ones"></a>bitset_count_ones()

숫자의 이진 표현에서 설정 비트 수를 반환 합니다.

```kusto
bitset_count_ones(42)
```

**구문**

`bitset_count_ones(`*num1*' ') '

**인수**

* *num1*: long 또는 integer 숫자입니다.

**반환**

숫자의 이진 표현에서 설정 비트 수를 반환 합니다.

**예제**

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
// 42 = 32+8+2 : b'00101010' == 3 bits set
print ones = bitset_count_ones(42) 
```

|만드세요|
|---|
|3|
