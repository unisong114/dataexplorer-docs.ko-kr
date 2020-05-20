---
title: 일괄 처리-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 일괄 처리에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: e5a38d53fb9b28fc7da0ddf71132e3a047e8188e
ms.sourcegitcommit: 974d5f2bccabe504583e387904851275567832e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/18/2020
ms.locfileid: "83550336"
---
# <a name="batches"></a>Batch

쿼리는 세미콜론 () 문자로 구분 되는 경우 여러 테이블 형식 식 문을 포함할 수 있습니다 `;` . 그런 다음 쿼리는 여러 테이블 형식 결과를 반환 합니다. 결과는 테이블 형식 식 문으로 생성 되 고 쿼리 텍스트의 문 순서에 따라 정렬 됩니다.

예를 들어 다음 쿼리는 두 개의 테이블 형식 결과를 생성 합니다. 그런 다음 사용자 에이전트 도구는 각각 및에 연결 된 적절 한 이름을 사용 하 여 해당 결과를 표시할 수 있습니다 `Count of events in Florida` `Count of events in Guam` .

```kusto
StormEvents | where State == "FLORIDA" | count | as ['Count of events in Florida'];
StormEvents | where State == "GUAM" | count | as ['Count of events in Guam']
```

Batch는 대시보드와 같은 여러 하위 쿼리에서 공통 계산을 공유 하는 시나리오에 유용 합니다. 일반적인 계산이 복잡 한 경우에는 [구체화 () 함수](./materializefunction.md) 를 사용 하 여 쿼리를 한 번만 실행 되도록 구성 합니다.

```kusto
let m = materialize(StormEvents | summarize n=count() by State);
m | where n > 2000;
m | where n < 10
```

메모:
* [`materialize`](materializefunction.md) [포크 연산자](forkoperator.md)를 사용 하 여 일괄 처리를 사용 하는 것이 좋습니다.
