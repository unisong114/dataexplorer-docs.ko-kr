---
title: series_stats_dynamic ()-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 series_stats_dynamic ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/10/2020
ms.openlocfilehash: 681e4e1b734fb9bf9d2357ec77be4ba1d61365dd
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92245918"
---
# <a name="series_stats_dynamic"></a>series_stats_dynamic()

동적 개체의 계열에 대 한 통계를 반환 합니다.  

`series_stats_dynamic()`함수는 동적 숫자 배열을 포함 하는 열을 입력으로 사용 하 고 다음 내용을 사용 하 여 동적 값을 생성 합니다.
* `min`: 입력 배열의 최 솟 값
* `min_idx`: 입력 배열에서 최 솟 값의 첫 번째 위치입니다.
* `max`: 입력 배열의 최 댓 값입니다.
* `max_idx`: 입력 배열에서 최 댓 값의 첫 번째 위치입니다.
* `avg`: 입력 배열의 평균 값입니다.
* `variance`: 입력 배열의 표본 분산
* `stdev`: 입력 배열의 샘플 표준 편차

## <a name="syntax"></a>구문

`series_stats_dynamic(`*x* `[,` *ignore_nonfinite*`])`

## <a name="arguments"></a>인수

* *x*: 숫자 값의 배열인 동적 배열 셀입니다. 
* *ignore_nonfinite*: `false` 한정 되지 않은 값 (*null*, *NaN*, *inf*등)을 무시 하 고 통계를 계산할지 여부를 지정 하는 부울 (선택 사항, 기본값:) 플래그입니다. `false`반환 된 결과로 설정 하면 한정 되지 `null` 않은 값이 배열에 있는 경우입니다.

## <a name="example"></a>예제

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print x=dynamic([23,46,23,87,4,8,3,75,2,56,13,75,32,16,29]) 
| project stats=series_stats_dynamic(x)
```

|stats
|---|
|{"min": 2.0, "min_idx": 8, "max": 87.0, "max_idx": 3, "avg": 32.8, "stdev": 28.503633853548269, "variance": 812.45714285714291}
