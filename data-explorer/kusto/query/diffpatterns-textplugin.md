---
title: diffpatterns_text 플러그인 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 diffpatterns_text 플러그인에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 58f059e2346dfb3f15bff295126a14a97f10f317
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81516017"
---
# <a name="diffpatterns_text-plugin"></a>diffpatterns_text 플러그인

문자열 값의 두 데이터 집합을 비교하고 두 데이터 집합 간의 차이점을 특징짓는 텍스트 패턴을 찾습니다.

```kusto
T | evaluate diffpatterns_text(TextColumn, BooleanCondition)
```

이 `diffpatterns_text` 텍스트 패턴은 두 집합에서 데이터의 서로 다른 부분을 캡처하는 텍스트 패턴 집합(예: 조건이 있을 때 `true` 많은 행을 캡처하는 패턴과 조건이 있는 경우 행의 낮은 백분율)을 반환합니다. `false` 패턴은 텍스트 열의 토큰 또는 와일드카드를 `*` 나타내는 토큰과 함께 연속 토큰(공백으로 구분됨)으로 빌드됩니다. 각 패턴은 결과의 행으로 표시됩니다.

**구문**

`T | evaluate diffpatterns_text(`텍스트 열, 부울 조건 [, 최소 토큰, 임계값, 최대 토큰]`)` 

**필수 인수**

* 텍스트 열 - *column_name*

    분석할 텍스트 열은 문자열 형식이어야 합니다.
    
* 부울 컨디션 - *부울 식*

    입력 테이블과 비교할 두 레코드 하위 집합을 생성하는 방법을 정의합니다. 알고리즘은 조건에 따라 쿼리를 "True" 및 "False"라는 두 데이터 집합으로 분할한 다음 쿼리 간의 (텍스트) 차이를 분석합니다. 

**선택적 인수**

다른 모든 인수는 선택 사항이지만, 아래와 같은 순서여야 합니다. 

* MinTokens - 0 < *int* < 200 [기본값: 1]

    결과 패턴당 와일드카드가 아닌 토큰의 최소 수를 설정합니다.

* 임계값 - 0.015 < *더블* < 1 [기본값: 0.05]

    두 집합 간의 최소 패턴(비율) 차이를 [설정합니다(diffpattern](diffpatternsplugin.md)참조).

* MaxTokens - 0 < *int* [기본값: 20]

    결과 패턴당 최대 토큰 수(처음부터)를 설정하고 하한을 지정하면 쿼리 런타임이 줄어듭니다.

**반환**

diffpatterns_text 결과는 다음 열을 반환합니다.

* Count_of_True: 조건이 있을 때 패턴과 일치하는 `true`행 수입니다.
* Count_of_False: 조건이 있을 때 패턴과 일치하는 `false`행 수입니다.
* Percent_of_True: 조건이 있을 때 행의 패턴과 일치하는 `true`행의 백분율입니다.
* Percent_of_False: 조건이 있는 경우 행의 패턴과 일치하는 `false`행의 백분율입니다.
* 패턴: 텍스트 문자열의 토큰과 와일드카드의 '를`*`포함하는 텍스트 패턴입니다. 

> [!NOTE]
> 패턴이 반드시 구별되는 것은 아니며 데이터 집합의 전체 범위를 제공하지 않을 수 있습니다. 패턴이 겹칠 수 있으며 일부 행이 패턴과 일치하지 않을 수 있습니다.

**예제**

```kusto
StormEvents     
| where EventNarrative != "" and monthofyear(StartTime) > 1 and monthofyear(StartTime) < 9
| where EventType == "Drought" or EventType == "Extreme Cold/Wind Chill"
| evaluate diffpatterns_text(EpisodeNarrative, EventType == "Extreme Cold/Wind Chill", 2)
```
|Count_of_True|Count_of_False|Percent_of_True|Percent_of_False|패턴|
|---|---|---|---|---|
|11|0|6.29|0|북서쪽으로 이동하는 바람 * 웨이크 * 표면 트로프가져 무거운 호수 효과 강설 바람 * 호수 수페리어에서|
|9|0|5.14|0|캐나다 고압 정착 * * 지역 * 2 월 * 2006 년 이후 가장 추운 온도 생산. 지속 시간 * 동결 온도|
|0|34|0|6.24|* * * * * * * * * * * * * * * * * * 웨스트 테네시|
|0|42|0|7.71|※ ※ ※ ※ ※ * * * * * * * * 서부 콜로라도 전역에 걸쳐 있습니다. *|
|0|45|0|8.26|* * 아래 정상 *|
|0|110|0|20.18|정상 이하 *|

