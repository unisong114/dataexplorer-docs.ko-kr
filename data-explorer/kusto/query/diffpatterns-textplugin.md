---
title: diffpatterns_text 플러그 인-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 diffpatterns_text 플러그 인을 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 9efa144335369c3d450fadb7ac92e3dec4c87865
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92249261"
---
# <a name="diffpatterns_text-plugin"></a>diffpatterns_text 플러그 인

문자열 값의 두 데이터 집합을 비교 하 고 두 데이터 집합 간의 차이를 규정 하는 텍스트 패턴을 찾습니다.

```kusto
T | evaluate diffpatterns_text(TextColumn, BooleanCondition)
```

는 `diffpatterns_text` 두 집합에 있는 데이터의 서로 다른 부분을 캡처하는 텍스트 패턴 집합을 반환 합니다. 즉, 조건이 인 경우 행의 많은 부분을 캡처하는 패턴이 며 `true` 조건이 일 때 행의 비율이 낮습니다. `false` 패턴은 공백으로 구분 된 연속 토큰에서 텍스트 열의 토큰이 나 와일드 카드를 나타내는로 작성 됩니다 `*` . 각 패턴은 결과의 행으로 표시됩니다.

## <a name="syntax"></a>구문

`T | evaluate diffpatterns_text(`TextColumn, BooleanCondition [, MinTokens, Threshold, MaxTokens]`)` 

## <a name="arguments"></a>인수

### <a name="required-arguments"></a>필수 인수

* TextColumn- *column_name*

    분석할 텍스트 열은 문자열 형식 이어야 합니다.
    
* BooleanCondition *식*

    입력 테이블과 비교할 두 개의 레코드 하위 집합을 생성 하는 방법을 정의 합니다. 알고리즘은 쿼리를 두 개의 데이터 집합 "True" 및 "False"로 분할 한 다음 두 데이터 집합 간의 차이 (텍스트)를 분석 합니다. 

### <a name="optional-arguments"></a>선택적 인수

다른 모든 인수는 선택 사항이지만, 아래와 같은 순서여야 합니다. 

* MinTokens-0 < *int* < 200 [기본값: 1]

    결과 패턴 당 비 와일드 카드 토큰의 최소 개수를 설정 합니다.

* 임계값-0.015 < *double* < 1 [기본값: 0.05]

    두 집합 간의 최소 패턴 (비율) 차이를 설정 합니다 ( [diffpatterns](diffpatternsplugin.md)참조).

* MaxTokens-0 < *int* [기본값: 20]

    결과 패턴 당 최대 토큰 수를 설정 하 고, 하한값을 지정 하면 쿼리 런타임이 감소 합니다.

## <a name="returns"></a>반환

Diffpatterns_text 결과는 다음 열을 반환 합니다.

* Count_of_True: 조건이 인 경우 패턴과 일치 하는 행의 수입니다 `true` .
* Count_of_False: 조건이 인 경우 패턴과 일치 하는 행의 수입니다 `false` .
* Percent_of_True: 조건이 인 경우 행에서 패턴과 일치 하는 행의 비율입니다 `true` .
* Percent_of_False: 조건이 인 경우 행에서 패턴과 일치 하는 행의 비율입니다 `false` .
* Pattern: 텍스트 문자열에서 토큰을 포함 하는 텍스트 패턴이 며 `*` 와일드 카드를 포함 합니다. 

> [!NOTE]
> 패턴은 반드시 다르지는 않으며 데이터 집합에 대 한 전체 검사를 제공 하지 않을 수 있습니다. 패턴은 겹칠 수 있으며 일부 행은 패턴과 일치 하지 않을 수 있습니다.

## <a name="example"></a>예제

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents     
| where EventNarrative != "" and monthofyear(StartTime) > 1 and monthofyear(StartTime) < 9
| where EventType == "Drought" or EventType == "Extreme Cold/Wind Chill"
| evaluate diffpatterns_text(EpisodeNarrative, EventType == "Extreme Cold/Wind Chill", 2)
```

|Count_of_True|Count_of_False|Percent_of_True|Percent_of_False|패턴|
|---|---|---|---|---|
|11|0|6.29|0|기간 동안 * wake *의 trough를 전환 하는 surface snowfall|
|9|0|5.14|0|캐나다 높은 압력 합의 * * region *은 2 월 * 2006 이후의 coldest 온도를 생성 했습니다. 기간 * 온도 고정|
|0|34|0|6.24|* * * * * * * * * * * * * * * * * * 서 부 테네시,|
|0|42|0|7.71|* * * * * * * * * * * * * * * Colorado *|
|0|45|0|8.26|* * normal 미만 *|
|0|110|0|20.18|일반 *|
