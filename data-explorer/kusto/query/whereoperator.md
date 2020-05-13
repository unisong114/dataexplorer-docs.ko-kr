---
title: where operator-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 where 연산자 (, contains, startswith, endswith, matches regex)를 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: fadf8aa8c21dac364793c73a38e68d55fc2a6f6d
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/13/2020
ms.locfileid: "83370364"
---
# <a name="where-operator"></a>where 연산자

조건자를 만족하는 행의 부분집합으로 테이블을 필터링합니다.

```kusto
T | where fruit=="apple"
```

**별칭**`filter`

**구문**

*T* `| where` *조건자*

**인수**

* *T*: 레코드를 필터링 할 테이블 형식 입력입니다.
* *Predicate*: `boolean` *T*의 열에 대 한 [식](./scalar-data-types/bool.md) 입니다. *T*의 각 행에 대해 평가 됩니다.

**반환**

*Predicate*가 `true`인 *T*의 행입니다.

**참고** Null 값: 모든 필터링 함수는 null 값과 비교 했을 때 false를 반환 합니다. 특수 null 인식 함수를 사용 하 여 null 값을 고려 하는 쿼리를 [isnull ()](./isnullfunction.md), [isnotnull ()](./isnotnullfunction.md), [isempty ()](./isemptyfunction.md), [isnotempty ()](./isnotemptyfunction.md)로 작성할 수 있습니다. 

**팁**

가장 빠른 성능을 얻으려면:

* **단순 비교를 사용** 합니다('상수'는 테이블에 대한 상수를 의미합니다. (' I n t i n s '는 테이블에 대 한 상수를 의미 하므로이는 `now()` `ago()` 정상 이므로 [ `let` 문을](./letstatement.md)사용 하 여 할당 된 스칼라 값입니다.)

    예를 들어 `where floor(Timestamp, 1d) == ago(1d)`보다 `where Timestamp >= ago(1d)`을(를) 선호합니다.

* **가장 단순한 용어를 먼저 사용**합니다. `and`을(를) 사용하여 여러 절을 결합하는 경우에는 열이 하나만 포함된 절을 앞쪽에 배치합니다. 따라서 `Timestamp > ago(1d) and OpId == EventId` 이 다른 방법보다 낫습니다.

자세한 내용은 [사용 가능한 문자열 연산자](./datatypes-string-operators.md) 의 요약 및 [사용 가능한 숫자 연산자](./numoperators.md)의 요약을 참조 하세요.

**예제**

```kusto
Traces
| where Timestamp > ago(1h)
    and Source == "MyCluster"
    and ActivityId == SubActivityId 
```

1 시간 보다 오래 된 레코드 이며 "MyCluster" 라는 원본에서 가져온 것 이며 값이 같은 두 개의 열이 있습니다. 

참고로 마지막 두 열은 인덱스를 이용할 수 없고 스캔을 강제로 실행하므로 해당 두 열 사이에 비교를 넣습니다.

**예제**

```kusto
Traces | where * has "Kusto"
```

"Kusto" 라는 단어가 모든 열에 표시 되는 모든 행입니다.
