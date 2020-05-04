---
title: 구체화 ()-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 구체화 ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/21/2019
ms.openlocfilehash: e721e5809d3b0445fecc0609668332b66ef39db8
ms.sourcegitcommit: d885c0204212dd83ec73f45fad6184f580af6b7e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82737337"
---
# <a name="materialize"></a>materialize()

다른 하위 쿼리가 부분 결과를 참조할 수 있는 방식으로 쿼리 실행 시 하위 쿼리 결과를 캐시할 수 있습니다.

 
**구문**

`materialize(`*식*`)`

**인수**

* *식*: 쿼리를 실행 하는 동안 평가 및 캐시할 테이블 형식 식입니다.

**팁**

* 피연산자에 한 번만 실행할 수 있는 상호 하위 쿼리가 있을 때 join 또는 union과 함께 구체화를 사용 합니다. 아래 예제를 참조하세요.

* 또한 시나리오에서 분기 기간을 조인/유니언해야 할 때 유용합니다.

* 구체화는 캐시 된 결과에 이름을 지정 하는 경우 let 문에서만 사용할 수 있습니다.


* 구체화의 캐시 크기 제한은 **5gb**입니다. 
  이 제한은 클러스터 노드당 이며 동시에 실행 되는 모든 쿼리에 대해 상호 합니다.
  쿼리에서를 사용 `materialize()` 하 고 캐시에서 더 이상 데이터를 보유할 수 없으면 쿼리가 오류와 함께 중단 됩니다.

**예**

임의의 값 집합을 생성 하 고 다음을 알아야 합니다. 
 * 보유 한 고유 값 수 
 * 이러한 모든 값의 합계입니다. 
 * 상위 3 개 값

[일괄 처리](batches.md) 및 구체화를 사용 하 여이 작업을 수행할 수 있습니다.

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