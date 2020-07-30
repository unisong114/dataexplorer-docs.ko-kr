---
title: series_stats ()-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 series_stats ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/10/2020
ms.openlocfilehash: 69e09ec9528e99e250986657bf5d7cc0756b14f8
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87351253"
---
# <a name="series_stats"></a>series_stats()

`series_stats()`여러 열의 계열에 대 한 통계를 반환 합니다.  

`series_stats()`함수는 동적 숫자 배열을 포함 하는 열을 입력으로 사용 하 고 다음 열을 계산 합니다.
* `min`: 입력 배열의 최 솟 값
* `min_idx`: 입력 배열에서 최 솟 값의 첫 번째 위치입니다.
* `max`: 입력 배열의 최 댓 값입니다.
* `max_idx`: 입력 배열에서 최 댓 값의 첫 번째 위치입니다.
* `avg`: 입력 배열의 평균 값입니다.
* `variance`: 입력 배열의 표본 분산
* `stdev`: 입력 배열의 샘플 표준 편차

> [!NOTE] 
> 이 함수는 여러 열을 반환 하므로 다른 함수의 인수로 사용할 수 없습니다.

## <a name="syntax"></a>Syntax

project `series_stats(` *x* `[,` *ignore_nonfinite* `])` 또는 extend `series_stats(` *x* `)` 는 앞에서 언급 한 모든 열을 series_stats_x_min, series_stats_x_min_idx 등의 이름으로 반환 합니다.
 
project (m, mi) = `series_stats(` *x* `)` 또는 extend (m, mi) = `series_stats(` *x* `)` 는 m (min) 및 mi (min_idx) 열을 반환 합니다.

## <a name="arguments"></a>인수

* *x*: 숫자 값 배열인 동적 배열 셀입니다. 
* *ignore_nonfinite*: `false` 한정 되지 않은 값 (*null*, *NaN*, *inf*등)을 무시 하 고 통계를 계산할지 여부를 지정 하는 부울 (선택 사항, 기본값:) 플래그입니다. 로 설정 하면 `false` `null` 한정 되지 않은 값이 배열에 있는 경우 반환 되는 값은입니다.

## <a name="example"></a>예제

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print x=dynamic([23,46,23,87,4,8,3,75,2,56,13,75,32,16,29]) 
| project series_stats(x)

```

|series_stats_x_min|series_stats_x_min_idx|series_stats_x_max|series_stats_x_max_idx|series_stats_x_avg|series_stats_x_stdev|series_stats_x_variance|
|---|---|---|---|---|---|---|
|2|8|87|3|32.8|28.5036338535483|812.457142857143|
