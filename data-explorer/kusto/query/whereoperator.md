---
title: Kusto 쿼리 언어의 where 연산자 - Azure Data Explorer
description: 이 문서에서는 Azure Data Explorer의 where 연산자에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.localizationpriority: high
ms.openlocfilehash: 6ac800cd4b38396e0f32f44976c4594c093747bb
ms.sourcegitcommit: f49e581d9156e57459bc69c94838d886c166449e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2020
ms.locfileid: "95512012"
---
# <a name="where-operator"></a>where 연산자

조건자를 만족하는 행의 부분집합으로 테이블을 필터링합니다.

```kusto
T | where fruit=="apple"
```

**별칭** `filter`

## <a name="syntax"></a>Syntax

*T* `| where` *Predicate*

## <a name="arguments"></a>인수

* *T*: 레코드를 필터링할 테이블 형식 입력입니다.
* *조건자*: *T* 의 열에 대한 `boolean` [식](./scalar-data-types/bool.md). *T* 의 각 행에 대해 계산됩니다.

## <a name="returns"></a>반환

*Predicate* 가 `true`인 *T* 의 행입니다.

**참고** Null 값: 모든 필터링 함수는 null 값과 비교할 때 false를 반환합니다. 특수 null 인식 함수를 사용하여 null 값을 처리하는 쿼리를 작성할 수 있습니다.

[isnull()](./isnullfunction.md), [isnotnull()](./isnotnullfunction.md), [isempty()](./isemptyfunction.md), [isnotempty()](./isnotemptyfunction.md). 

**팁**

가장 빠른 성능을 얻으려면:

* **단순 비교를 사용** 합니다('상수'는 테이블에 대한 상수를 의미합니다. ('상수'는 테이블에 대한 상수를 의미합니다. 따라서 `now()` 및 `ago()`는 정상이며, [`let` 문](./letstatement.md)을 사용하여 할당된 스칼라도 마찬가지입니다.)

    예를 들어 `where floor(Timestamp, 1d) == ago(1d)`보다 `where Timestamp >= ago(1d)`을(를) 선호합니다.

* **가장 단순한 용어를 먼저 사용** 합니다. `and`을(를) 사용하여 여러 절을 결합하는 경우에는 열이 하나만 포함된 절을 앞쪽에 배치합니다. 따라서 `Timestamp > ago(1d) and OpId == EventId` 이 다른 방법보다 낫습니다.

자세한 내용은 [사용 가능한 문자열 연산자](./datatypes-string-operators.md) 요약 및 [사용 가능한 숫자 연산자](./numoperators.md) 요약을 참조하세요.

## <a name="example-simple-comparisons-first"></a>예제: 간단한 첫 번째 비교

```kusto
Traces
| where Timestamp > ago(1h)
    and Source == "MyCluster"
    and ActivityId == SubActivityId 
```

이 예제에서는 1시간 이하의 레코드를 검색하고 `MyCluster`라는 원본에서 가져온 것이며, 같은 값의 두 개의 열을 가지고 있습니다. 

마지막 두 열은 인덱스를 사용할 수 없고 스캔을 강제로 적용할 수 없으므로 해당 두 열 사이에 비교를 넣습니다.

## <a name="example-columns-contain-string"></a>예제: 열에 문자열 포함

```kusto
Traces | where * has "Kusto"
```

"Kusto"라는 단어가 모든 열에 표시되는 모든 행입니다.
 
