---
title: 구체화() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기에서 materialize()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/21/2019
ms.openlocfilehash: dfaed8cf972a517c86717999b3e5423c0ddd1fb0
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81512617"
---
# <a name="materialize"></a>materialize()

다른 하위 쿼리가 부분 결과를 참조할 수 있는 방식으로 쿼리 실행 시 하위 쿼리 결과를 캐싱할 수 있습니다.

 
**구문**

`materialize(`*식*`)`

**인수**

* *식*: 쿼리 실행 중에 평가되고 캐시되는 테이블 형식식입니다.

**팁**

* 피연산자가 한 번 실행할 수 있는 상호 하위 쿼리를 갖는 조인/유니언이 있는 경우 구체화를 사용합니다.

* 또한 시나리오에서 분기 기간을 조인/유니언해야 할 때 유용합니다.

* 구체화는 캐시된 결과에 이름을 부여함으로써 let 문에서만 사용하도록 허용되어 있습니다.

* Materialize에는 **5GB의**캐시 크기 제한이 있습니다. 
  이 제한은 클러스터 노드당이며 동시에 실행되는 모든 쿼리에 대해 상호 입니다.
  쿼리가 사용하고 `materialize()` 캐시에 추가 데이터를 보유할 수 없는 경우 쿼리는 오류와 함께 중단됩니다.

**예**

임의의 값 집합을 생성하려는 경우 이러한 모든 값과 상위 3개 값의 합계를 얼마나 구별할 수 있는지 를 찾는 데 관심이 있습니다.

이것은 [배치를](batches.md) 사용하여 수행하고 구체화 할 수 있습니다 :

 ```kusto
let randomSet = materialize(range x from 1 to 30000000 step 1
| project value = rand(10000000));
randomSet
| summarize dcount(value);
randomSet
| top 3 by value;
randomSet
| summarize sum(value)

```