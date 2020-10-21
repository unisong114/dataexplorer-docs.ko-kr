---
title: sort 연산자-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 sort 연산자에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 8823b0a6bb15898a9bb15ed00919fa57d75f8e25
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92245811"
---
# <a name="sort-operator"></a>sort 연산자 

입력 테이블의 행을 하나 이상의 열에 의해 순서대로 정렬합니다.

```kusto
T | sort by strlen(country) asc, price desc
```

**별칭**

`order`

## <a name="syntax"></a>구문

*T* `| sort by` *식* [ `asc`  |  `desc` ] [ `nulls first`  |  `nulls last` ] [ `,` ...]

## <a name="arguments"></a>인수

* *T*: 정렬할 테이블 입력입니다.
* *expression*: 정렬할 스칼라 식입니다. 값의 형식은 숫자, 날짜, 시간 또는 문자열이어야 합니다.
* `asc` 오름차순으로, 즉 낮은 값에서 높은 값의 순서로 정렬합니다. 기본값은 `desc`내림차순, 즉 높은 값에서 낮은 값으로 정렬하는 것입니다.
* `nulls first` (order에 대 한 기본값 `asc` )은 null 값을 시작 및 `nulls last` (order의 기본값 `desc` )은 끝에 null 값을 삽입 합니다.

## <a name="example"></a>예제

```kusto
Traces
| where ActivityId == "479671d99b7b"
| sort by Timestamp asc nulls first
```

특정 `ActivityId`가 지정된 Traces 테이블 내의 모든 행을 타임스탬프 기준으로 정렬하여 반환합니다. `Timestamp`열이 null 값을 포함 하는 경우 결과의 첫 번째 줄에 표시 됩니다.

결과에서 null 값을 제외 하려면 정렬에 대 한 호출 앞에 필터 추가를 추가 합니다.

```kusto
Traces
| where ActivityId == "479671d99b7b" and isnotnull(Timestamp)
| sort by Timestamp asc
```