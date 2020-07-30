---
title: sample distinct 연산자-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 샘플 고유 연산자에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 3cb1de08604964d4d71c5868ef7564c728b1f2c4
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87351542"
---
# <a name="sample-distinct-operator"></a>sample-distinct 연산자

요청된 열의 고유 값을 지정된 수만큼 포함하는 단일 열을 반환합니다. 

연산자의 기본 (및 현재만 해당) 버전은 가능한 한 빨리 (공평 하 게 샘플을 만드는 대신) 답변을 반환 하려고 시도 합니다.

```kusto
T | sample-distinct 5 of DeviceId
```

## <a name="syntax"></a>Syntax

*T* `| sample-distinct` *numberofvalues* `of` *ColumnName*

## <a name="arguments"></a>인수
* *Numberofvalues*: 반환할 *T* 의 고유 값 수입니다. 임의의 숫자 식을 지정할 수 있습니다.

**팁**

 `sample-distinct`연산자를 사용 하 여 let 문과 나중에 필터를 추가 하 여 모집단을 샘플링 하는 것이 편리할 수 있습니다 `in` (예제 참조). 

 단지 샘플이 아닌 상위 값을 원하는 경우 [hitters](tophittersoperator.md) 연산자를 사용할 수 있습니다. 

 특정 열 값이 아닌 데이터 행을 샘플링 하려면 [sample 연산자](sampleoperator.md) 를 참조 하세요.

## <a name="examples"></a>예제  

모집단에서 10 개의 고유 값 가져오기

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents | sample-distinct 10 of EpisodeId

```

모집단을 샘플링하고 추가 계산을 수행하여 합계가 쿼리 한도를 초과하지 않을지 알아냅니다. 

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let sampleEpisodes = StormEvents | sample-distinct 10 of EpisodeId;
StormEvents 
| where EpisodeId in (sampleEpisodes) 
| summarize totalInjuries=sum(InjuriesDirect) by EpisodeId
```
