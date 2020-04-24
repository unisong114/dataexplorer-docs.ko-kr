---
title: 정렬 연산자 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 정렬 연산자에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 638783b28cddc51d64a80096d7d4d6e0f669d354
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81507483"
---
# <a name="sort-operator"></a>sort 연산자 

입력 테이블의 행을 하나 이상의 열에 의해 순서대로 정렬합니다.

```kusto
T | sort by strlen(country) asc, price desc
```

**별칭**

`order`

**구문**

*T* `| sort by` *expression* 식`asc`[ [ | `desc`[ [`nulls first` | `nulls last`[ [ [ [...]`,`

**인수**

* *T*: 정렬할 테이블 입력입니다.
* *식*: 정렬할 스칼라 식입니다. 값의 형식은 숫자, 날짜, 시간 또는 문자열이어야 합니다.
* `asc` 오름차순으로, 즉 낮은 값에서 높은 값의 순서로 정렬합니다. 기본값은 `desc`내림차순, 즉 높은 값에서 낮은 값으로 정렬하는 것입니다.
* `nulls first`(순서에 `asc` 대한 기본값)은 처음에 null `nulls last` 값을 배치하고 `desc` (순서의 기본값) 끝에 null 값을 배치합니다.

**예제**

```kusto
Traces
| where ActivityId == "479671d99b7b"
| sort by Timestamp asc nulls first
```

특정 `ActivityId`가 지정된 Traces 테이블 내의 모든 행을 타임스탬프 기준으로 정렬하여 반환합니다. 열에 null 값이 포함된 경우 `Timestamp` 해당 값이 결과의 첫 번째 줄에 표시됩니다.

결과에서 null 값을 제외하려면 정렬 할 호출 앞에 필터를 추가합니다.

```kusto
Traces
| where ActivityId == "479671d99b7b" and isnotnull(Timestamp)
| sort by Timestamp asc
```