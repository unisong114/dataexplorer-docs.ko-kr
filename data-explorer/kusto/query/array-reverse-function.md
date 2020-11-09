---
title: array_reverse ()-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 array_reverse ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: slneimer
ms.service: data-explorer
ms.topic: reference
ms.date: 11/09/2020
ms.openlocfilehash: 327564a953c8f537a0f76727b4313749f61eec3e
ms.sourcegitcommit: 4b061374c5b175262d256e82e3ff4c0cbb779a7b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/09/2020
ms.locfileid: "94374013"
---
# <a name="array_reverse"></a>array_reverse ()

동적 배열에서 요소의 순서를 반대로 바꿉니다.

## <a name="syntax"></a>구문

`array_reverse(`*array*`)`

## <a name="arguments"></a>인수

*array* : 되돌릴 입력 배열입니다.

## <a name="returns"></a>반환

입력 배열과 정확히 동일한 요소를 포함 하는 배열을 역순으로 포함 합니다.

## <a name="example"></a>예제

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print arr=dynamic(["this", "is", "an", "example"]) 
| project Result=array_reverse(arr)
```

|결과|
|---|
|["example", "a", "is", "this"]|
