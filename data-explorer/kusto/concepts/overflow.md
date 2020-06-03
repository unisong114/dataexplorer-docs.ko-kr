---
title: 오버플로-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 오버플로에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 063165c91319ef5f4183a8ce8f83364fd8188072
ms.sourcegitcommit: f7101c6b41ec250d05f4cb6092e2939958b37b40
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/03/2020
ms.locfileid: "84328962"
---
# <a name="overflows"></a>오버플로

계산 결과가 대상 형식에 비해 너무 클 경우 오버플로가 발생 합니다.
오버플로는 일반적으로 [부분 쿼리 실패](partialqueryfailures.md)를 초래 합니다.

예를 들어 다음 쿼리는 오버플로를 발생 합니다.

```kusto
let Weight = 92233720368547758;
range x from 1 to 3 step 1
| summarize percentilesw(x, Weight * 100, 50)
```

Kusto의 `percentilesw()` 구현은 `Weight` "충분히 가까이" 값에 대 한 식을 누적 합니다.
이 경우 누적은 부호 있는 64 비트 정수로 들어가지 않으므로 오버플로를 트리거합니다.

일반적으로 Kusto는 산술 계산에 64 비트 형식을 사용 하기 때문에 오버플로는 쿼리에 "bug"의 결과입니다.
가장 좋은 조치는 오류 메시지를 확인 하 고 오버플로를 트리거한 함수 또는 집계를 식별 하는 것입니다. 입력 인수가 적합 한 값으로 평가 되는지 확인 합니다.
