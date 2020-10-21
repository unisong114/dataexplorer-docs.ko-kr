---
title: bitset_count_ones ()-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 bitset_count_ones ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/22/2020
ms.openlocfilehash: fb14ffa5807a61ade913631c7d6da6b23edc26d9
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92245332"
---
# <a name="bitset_count_ones"></a>bitset_count_ones()

숫자의 이진 표현에서 설정 비트 수를 반환 합니다.

```kusto
bitset_count_ones(42)
```

## <a name="syntax"></a>구문

`bitset_count_ones(`*num1*' ') '

## <a name="arguments"></a>인수

* *num1*: long 또는 integer 숫자입니다.

## <a name="returns"></a>반환

숫자의 이진 표현에서 설정 비트 수를 반환 합니다.

## <a name="example"></a>예제

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
// 42 = 32+8+2 : b'00101010' == 3 bits set
print ones = bitset_count_ones(42) 
```

|만드세요|
|---|
|3|
