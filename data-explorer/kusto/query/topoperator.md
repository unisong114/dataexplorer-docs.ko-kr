---
title: top 연산자-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 top 연산자에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: aadb0b41e666b5b0e90fa33b7ddc30e363e985c3
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92241240"
---
# <a name="top-operator"></a>top 연산자

지정 된 열을 기준으로 정렬 된 처음 *N 개* 레코드를 반환 합니다.

```kusto
T | top 5 by Name desc nulls last
```

## <a name="syntax"></a>구문

*T* `| top` *numberofrows* `by` *식* [ `asc`  |  `desc` ] [ `nulls first`  |  `nulls last` ]

## <a name="arguments"></a>인수

* *Numberofrows*: 반환할 *T* 의 행 수입니다. 임의의 숫자 식을 지정할 수 있습니다.
* *Expression*: 정렬할 스칼라 식입니다. 값의 형식은 숫자, 날짜, 시간 또는 문자열이어야 합니다.
* 선택이 실제로 범위의 "맨 아래"에서 시작되는지 아니면 "맨 위"에서 시작되는지를 제어하기 위해 `asc` 또는 `desc`(기본값)가 나타날 수 있습니다.
* `nulls first` (order의 기본값 `asc` ) 또는 `nulls last` (order의 기본값 `desc` )은 null 값이 범위의 시작 부분이 나 끝 부분에 있는지 여부를 제어 하는 것 처럼 보일 수 있습니다.

> [!TIP]
> `top 5 by name` 은 `sort by name | take 5` 의미 체계 및 성능 관점 모두에서 식에 해당 합니다.

## <a name="see-also"></a>참조 

* [상위 중첩](topnestedoperator.md) 연산자를 사용 하 여 계층적 (중첩 된) 상위 결과를 생성 합니다.
