---
title: 오버플로 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 오버플로에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 22db905788e1313cad2eb229239a390c28bcd5c2
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81523157"
---
# <a name="overflows"></a>오버플로

오버플로는 계산 결과가 대상 유형에 비해 너무 큰 경우 발생합니다.
이 현상은 일반적으로 [부분 쿼리 실패로](partialqueryfailures.md)이어집니다.

예를 들어 다음 쿼리로 인해 오버플로가 발생합니다.

```kusto
let Weight = 92233720368547758;
range x from 1 to 3 step 1
| summarize percentilesw(x, Weight * 100, 50)
```

Kusto의 `percentilesw()` 구현은 "충분히 가까운" 값에 대한 `Weight` 표현식을 축적합니다.
이 경우 누적은 서명된 64비트 정수에 맞지 않기 때문에 오버플로를 트리거합니다.

그러나 Kusto는 산술 계산에 64비트 형식을 사용하기 때문에 일반적으로 오버플로는 쿼리에서 "버그"의 결과입니다.
이러한 경우 가장 좋은 작업 과정은 함수 또는 집계가 오버플로를 트리거한 오류 메시지에서 식별하고 입력 인수가 의미 있는 값으로 평가되도록 하는 것입니다.
