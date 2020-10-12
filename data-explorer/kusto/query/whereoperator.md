---
title: Kusto 쿼리 언어의 where 연산자-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 where 연산자에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 79eb1314fad1c3d3bdbbda242b3bd6a86c19e158
ms.sourcegitcommit: 6f610cd9c56dbfaff4eb0470ac0d1441211ae52d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/12/2020
ms.locfileid: "91954470"
---
# <a name="where-operator"></a>where 연산자

조건자를 만족하는 행의 부분집합으로 테이블을 필터링합니다.

```kusto
T | where fruit=="apple"
```

**별칭**`filter`

## <a name="syntax"></a>구문

*T* `| where` *조건자*

## <a name="arguments"></a>인수

* *T*: 레코드를 필터링 할 테이블 형식 입력입니다.
* *Predicate*: `boolean` *T*의 열에 대 한 [식](./scalar-data-types/bool.md) 입니다. *T*의 각 행에 대해 평가 됩니다.

## <a name="returns"></a>반환

*Predicate*가 `true`인 *T*의 행입니다.

**참고** Null 값: 모든 필터링 함수는 null 값과 비교 했을 때 false를 반환 합니다. 특수 null 인식 함수를 사용 하 여 null 값을 처리 하는 쿼리를 작성할 수 있습니다.

[isnull ()](./isnullfunction.md), [isnotnull ()](./isnotnullfunction.md), [isempty ()](./isemptyfunction.md), [isnotempty ()](./isnotemptyfunction.md). 

**팁**

가장 빠른 성능을 얻으려면:

* **단순 비교를 사용** 합니다('상수'는 테이블에 대한 상수를 의미합니다. (' I n t i n s '는 테이블에 대 한 상수를 의미 하므로이는 `now()` `ago()` 정상 이므로 [ `let` 문을](./letstatement.md)사용 하 여 할당 된 스칼라 값입니다.)

    예를 들어 `where floor(Timestamp, 1d) == ago(1d)`보다 `where Timestamp >= ago(1d)`을(를) 선호합니다.

* **가장 단순한 용어를 먼저 사용**합니다. `and`을(를) 사용하여 여러 절을 결합하는 경우에는 열이 하나만 포함된 절을 앞쪽에 배치합니다. 따라서 `Timestamp > ago(1d) and OpId == EventId` 이 다른 방법보다 낫습니다.

자세한 내용은 [사용 가능한 문자열 연산자](./datatypes-string-operators.md) 의 요약 및 [사용 가능한 숫자 연산자](./numoperators.md)의 요약을 참조 하세요.

## <a name="example-simple-comparisons-first"></a>예: 먼저 간단한 비교

```kusto
Traces
| where Timestamp > ago(1h)
    and Source == "MyCluster"
    and ActivityId == SubActivityId 
```

이 예제에서는 1 시간 보다 오래 된 레코드를 검색 하 고, 라는 소스에서 가져온 `MyCluster` 다음, 값이 같은 두 개의 열을 포함 합니다. 

인덱스를 사용할 수 없고 검색을 강제 적용할 수 없기 때문에 두 열을 마지막에 비교 합니다.

## <a name="example-columns-contain-string"></a>예: 열에 문자열이 포함 됩니다.

```kusto
Traces | where * has "Kusto"
```

"Kusto" 라는 단어가 모든 열에 표시 되는 모든 행입니다.
 
