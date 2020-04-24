---
title: 여기서 연산자 (포함, 시작, 끝, 정규식 일치) - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure Data Explorer에서 연산자(정수와 일치, 시작,끝)에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: a0e1486691740600fb5de4316982e8d43b13ce3a
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81504304"
---
# <a name="where-operator-has-contains-startswith-endswith-matches-regex"></a>여기서 연산자 (포함, 시작, 끝, 정규식 일치)

조건자를 만족하는 행의 부분집합으로 테이블을 필터링합니다.

```kusto
T | where fruit=="apple"
```

**별칭**`filter`

**구문**

*T* `| where` *술어*

**인수**

* *T*: 레코드를 필터링할 테이블 형식 입력입니다.
* *조건자*: `boolean` [expression](./scalar-data-types/bool.md) *T*. *T.* 각 행에 대해 평가됩니다.

**반환**

*Predicate*가 `true`인 *T*의 행입니다.

**참고 사항** Null 값: null 값과 비교할 때 모든 필터링 함수는 false를 반환합니다. 특수 null 인식 함수를 사용하여 null 값을 고려하는 쿼리를 작성할 수 있습니다: [isnull()](./isnullfunction.md)- [isnotnull() - isnotnull()](./isnotnullfunction.md)- [isnotempty()](./isemptyfunction.md)- [isnotempty()](./isnotemptyfunction.md). 

**팁**

가장 빠른 성능을 얻으려면:

* **단순 비교를 사용** 합니다('상수'는 테이블에 대한 상수를 의미합니다. ('상수'는 테이블 위에 `now()` 상수를 의미하므로 `ago()` OK이므로 [ `let` 문으로](./letstatement.md)할당 된 스칼라 값도 있습니다.)

    예를 들어 `where floor(Timestamp, 1d) == ago(1d)`보다 `where Timestamp >= ago(1d)`을(를) 선호합니다.

* **가장 단순한 용어를 먼저 사용**합니다. `and`을(를) 사용하여 여러 절을 결합하는 경우에는 열이 하나만 포함된 절을 앞쪽에 배치합니다. 따라서 `Timestamp > ago(1d) and OpId == EventId` 이 다른 방법보다 낫습니다.

자세한 내용은 사용 가능한 문자열 [연산자의](./datatypes-string-operators.md) 요약 및 [사용 가능한 숫자 연산자의 요약을](./numoperators.md)참조하십시오.

**예제**

```kusto
Traces
| where Timestamp > ago(1h)
    and Source == "MyCluster"
    and ActivityId == SubActivityId 
```

1시간 미만이고 "MyCluster"라는 소스에서 온 레코드이며 동일한 값의 두 열이 있습니다. 

참고로 마지막 두 열은 인덱스를 이용할 수 없고 스캔을 강제로 실행하므로 해당 두 열 사이에 비교를 넣습니다.

**예제**

```kusto
Traces | where * has "Kusto"
```

"Kusto"라는 단어가 모든 열에 표시되는 모든 행입니다.