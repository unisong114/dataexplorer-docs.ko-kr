---
title: 상단 연산자 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 상위 연산자에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: bb1d24dd0cd1dfeecb1925e474eb77e8cb86121f
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81505919"
---
# <a name="top-operator"></a>top 연산자

지정한 열을 기준으로 정렬된 처음 *N* 개 레코드를 반환합니다.

```kusto
T | top 5 by Name desc nulls last
```

**구문**

*T* `| top` *번호Ofrows* `by` `asc` | `desc``nulls first` | `nulls last` *표현식* [ [ [ [ ]

**인수**

* *NumberOfRows*: 반환할 *T행의* 수입니다. 숫자 식을 지정할 수 있습니다.
* *표현식*: 정렬할 스칼라 식입니다. 값의 형식은 숫자, 날짜, 시간 또는 문자열이어야 합니다.
* 선택이 실제로 범위의 "맨 아래"에서 시작되는지 아니면 "맨 위"에서 시작되는지를 제어하기 위해 `asc` 또는 `desc`(기본값)가 나타날 수 있습니다.
* `nulls first`(순서에 `asc` 대한 기본값) `nulls last` 또는 `desc` (순서에 대한 기본값) null 값이 범위의 시작 또는 끝에 있는지 여부를 제어하는 것처럼 보일 수 있습니다.


**팁**

* `top 5 by name`는 의미 체계 `sort by name | take 5` 및 성능 관점에서 모두 표현식과 동일합니다.
* [최상위 중첩](topnestedoperator.md) 연산을 사용하여 계층적(중첩된) 상위 결과를 생성합니다.