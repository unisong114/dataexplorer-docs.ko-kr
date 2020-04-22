---
title: 샘플 별개 연산자 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기에서 샘플 구분 연산자에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: b6d6c77aef3a7e2c6d99af792062d9f1a6215f51
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663645"
---
# <a name="sample-distinct-operator"></a>sample-distinct 연산자

요청된 열의 고유 값을 지정된 수만큼 포함하는 단일 열을 반환합니다. 

운영자의 기본 (현재만) 맛은 가능한 한 빨리 답변을 반환하려고시도합니다 (공정한 샘플을 만들기 위해 노력하는 대신).

```kusto
T | sample-distinct 5 of DeviceId
```

**구문**

*T* `| sample-distinct` *번호Ofvalues* `of` *열 이름*

**인수**
* *NumberOfValues*: 반환할 *T의* 고유 값입니다. 숫자 식을 지정할 수 있습니다.

**팁**

 let 문을 넣고 연산자를 `sample-distinct` 사용하여 나중에 필터를 입력하여 모집단을 `in` 샘플링하는 데 편리할 수 있습니다(예제 참조). 

 샘플이 아닌 최상위 값을 원한다면 [최상위 타자 연산자(상위 타자](tophittersoperator.md) 연산자)를 사용할 수 있습니다. 

 특정 열의 값이 아닌 데이터 행을 샘플링하려면 샘플 [연산자](sampleoperator.md)

**예**  

모집단에서 10개의 고유한 값 받기

```kusto
StormEvents | sample-distinct 10 of EpisodeId

```

모집단을 샘플링하고 추가 계산을 수행하여 합계가 쿼리 한도를 초과하지 않을지 알아냅니다. 

```kusto
let sampleEpisodes = StormEvents | sample-distinct 10 of EpisodeId;
StormEvents 
| where EpisodeId in (sampleEpisodes) 
| summarize totalInjuries=sum(InjuriesDirect) by EpisodeId
```