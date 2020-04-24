---
title: series_stats_dynamic() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 series_stats_dynamic()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/10/2020
ms.openlocfilehash: b667af6d037b0b316bf18406e1fb49528e390213
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81507908"
---
# <a name="series_stats_dynamic"></a>series_stats_dynamic()

동적 개체에서 계열에 대한 통계를 반환합니다.  

함수는 `series_stats_dynamic()` 동적 숫자 배열을 포함하는 열을 입력으로 가져와 다음 내용으로 동적 값을 생성합니다.
* `min`: 입력 배열의 최소값
* `min_idx`: 입력 배열의 최소값의 첫 번째 위치
* `max`: 입력 배열의 최대값
* `max_idx`: 입력 배열에서 최대값의 첫 번째 위치
* `avg`: 입력 배열의 평균 값
* `variance`: 입력 어레이의 샘플 분산
* `stdev`: 입력 어레이의 샘플 표준 편차

**구문**

`series_stats_dynamic(`*x* `[,` *ignore_nonfinite*`])`

**인수**

* *x*: 숫자 값의 배열인 동적 배열 셀입니다. 
* *ignore_nonfinite*: Boolean (선택 `false`사항, 기본값 : ) 비유한 값을 무시하고 통계를 계산할지 여부를 지정하는 플래그 *(null,* *NaN*, *inf*등). 반환된 `false` 결과로 설정하면 `null` 유한하지 않은 값이 배열에 있는 경우입니다.

**예제**

```kusto
print x=dynamic([23,46,23,87,4,8,3,75,2,56,13,75,32,16,29]) 
| project stats=series_stats_dynamic(x)
```

|stats
|---|
|{"min": 2.0, "min_idx": 8, "최대": 87.0, "max_idx": 3, "평균": 32.8, "stdev": 28.50363385548269, "분산": 812.45714285774291 }