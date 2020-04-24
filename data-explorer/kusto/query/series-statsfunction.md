---
title: series_stats() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 series_stats()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/10/2020
ms.openlocfilehash: 07aa5df7351a5d4be1522d39456423197bde508d
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81507925"
---
# <a name="series_stats"></a>series_stats()

`series_stats()`은 여러 열의 계열에 대한 통계를 반환합니다.  

함수는 `series_stats()` 동적 숫자 배열을 포함하는 열을 입력으로 사용하여 다음 열을 계산합니다.
* `min`: 입력 배열의 최소값
* `min_idx`: 입력 배열의 최소값의 첫 번째 위치
* `max`: 입력 배열의 최대값
* `max_idx`: 입력 배열에서 최대값의 첫 번째 위치
* `avg`: 입력 배열의 평균 값
* `variance`: 입력 어레이의 샘플 분산
* `stdev`: 입력 어레이의 샘플 표준 편차

> [!NOTE] 
> 이 함수는 여러 열을 반환하므로 다른 함수에 대한 인수로 사용할 수 없습니다.

**구문**

프로젝트 `series_stats(` *x* `[,` *ignore_nonfinite* `])` 또는 `series_stats(`확장 *x* `)` series_stats_x_min, series_stats_x_min_idx 등 위에서 언급 한 모든 열을 반환합니다.
 
프로젝트 (m,`series_stats(`mi)=*x* `)` 또는 확장`series_stats(`(m, mi)=*x* `)` 다음 열을 반환합니다: m (min) 및 mi (min_idx).

**인수**

* *x*: 숫자 값의 배열인 동적 배열 셀입니다. 
* *ignore_nonfinite*: Boolean (선택 `false`사항, 기본값 : ) 비유한 값을 무시하고 통계를 계산할지 여부를 지정하는 플래그 *(null,* *NaN*, *inf*등). 을 설정하면 `false`반환된 값은 유한하지 않은 값이 배열에 있는 `null` 경우입니다.

**예제**

```kusto
print x=dynamic([23,46,23,87,4,8,3,75,2,56,13,75,32,16,29]) 
| project series_stats(x)

```

|series_stats_x_min|series_stats_x_min_idx|series_stats_x_max|series_stats_x_max_idx|series_stats_x_avg|series_stats_x_stdev|series_stats_x_variance|
|---|---|---|---|---|---|---|
|2|8|87|3|32.8|28.5036338535483|812.457142857143|
