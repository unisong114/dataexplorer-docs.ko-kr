---
title: 일괄 처리 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 일괄 처리에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: bd695a1e7ffd9980de2750d38ad9538eeb877538
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81518023"
---
# <a name="batches"></a>Batch

쿼리에는 세미콜론()`;`문자로 구분되는 한 여러 개의 테이블 형식 식 문이 포함될 수 있습니다. 그런 다음 쿼리는 테이블 형식 식 문에 의해 생성되고 쿼리 텍스트의 명령문에 따라 정렬된 여러 테이블 형식 결과를 반환합니다.

예를 들어 다음 쿼리에서는 두 개의 테이블 형식 결과를 생성합니다. 그런 다음 사용자 에이전트 도구는 각각 (및`Count of events in Florida` 및 `Count of events in Guam`, 각각)과 연결된 적절한 이름으로 해당 결과를 표시할 수 있습니다.

```kusto
StormEvents | where State == "FLORIDA" | count | as ['Count of events in Florida'];
StormEvents | where State == "GUAM" | count | as ['Count of events in Guam']
```

일괄 처리는 대시보드와 같이 여러 하위 쿼리에서 공유하는 공통 계산이 있는 시나리오에 특히 유용합니다. 일반적인 계산이 복잡한 경우 [materialize() 함수를](./materializefunction.md)사용하여 쿼리를 한 번만 실행되도록 쿼리를 구성하는 것이 좋습니다.

```kusto
let m = materialize(StormEvents | summarize n=count() by State);
m | where n > 2000;
m | where n < 10
```

참고:
* 포크 [`materialize`](materializefunction.md) [연산자](forkoperator.md)를 사용하여 일괄 처리 및 이상을 선호합니다.