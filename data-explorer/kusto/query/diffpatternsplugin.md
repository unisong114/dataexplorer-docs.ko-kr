---
title: diffpatterns 플러그 인-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 diffpatterns plugin에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: a02f275dc47e88c7b14b85d19040e907613d1b80
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348329"
---
# <a name="diff-patterns-plugin"></a>diff 패턴 플러그 인

동일한 구조체의 두 데이터 집합을 비교 하 고 두 데이터 집합 간의 차이를 특징으로 하는 불연속 특성 (차원)의 패턴을 찾습니다.
 `Diffpatterns`는 오류를 분석 하는 데 도움이 되도록 개발 되었지만 (예를 들어 지정 된 시간 프레임 내에 오류가 아닌 오류를 비교 하 여) 같은 구조의 두 데이터 집합 간에 차이가 있을 수 있습니다. 

```kusto
T | evaluate diffpatterns(splitColumn)
```


## <a name="syntax"></a>Syntax

`T | evaluate diffpatterns(SplitColumn, SplitValueA, SplitValueB [, WeightColumn, Threshold, MaxDimensions, CustomWildcard, ...])` 

**필수 인수**

* SplitColumn - *column_name*

    알고리즘에 쿼리를 데이터 집합으로 분할하는 방법을 알려줍니다. SplitValueA 및 SplitValueB 인수(아래 참조)에 지정된 값에 따라 알고리즘은 쿼리를 두 개의 데이터 집합 “A” 및 “B”로 분할하고 둘 사이의 차이점을 분석합니다. 따라서 분할 열은 둘 이상의 고유 값을 가져야 합니다.

* SplitValueA - *string*

    지정된 SplitColumn에 있는 값 중 하나를 문자열로 나타낸 것입니다. SplitColumn에이 값이 있는 모든 행은 데이터 집합 "A"로 간주 됩니다.

* SplitValueB - *string*

    지정된 SplitColumn에 있는 값 중 하나를 문자열로 나타낸 것입니다. SplitColumn에이 값이 있는 모든 행은 데이터 집합 "B"로 간주 됩니다.

    예: `T | extend splitColumn=iff(request_responseCode == 200, "Success" , "Failure") | evaluate diffpatterns(splitColumn, "Success","Failure") `

**선택적 인수**

다른 모든 인수는 선택 사항이지만, 아래와 같은 순서여야 합니다. 기본값을 사용해야 함을 나타내려면 문자열 물결표 값 '~'을 사용합니다(아래 예 참조).

* WeightColumn - *column_name*

    지정된 가중치(기본적으로 각 행의 가중치는 '1'임)에 따라 입력의 각 행을 고려합니다. 인수는 숫자 열의 이름 이어야 합니다 (예: `int` ,, `long` `real` ).
    이미 각 행에 포함되어 있는 데이터의 계정 샘플링 또는 버킷팅/집계가 가중치 열의 일반적인 용도로 간주됩니다.
    
    예: `T | extend splitColumn=iff(request_responseCode == 200, "Success" , "Failure") | evaluate diffpatterns(splitColumn, "Success","Failure", sample_Count) `

* 임계값-0.015 < *double* < 1 [기본값: 0.05]

    두 집합 간의 최소 패턴(비율) 차이를 설정합니다.

    예제: `T | extend splitColumn = iff(request-responseCode == 200, "Success" , "Failure") | evaluate diffpatterns(splitColumn, "Success","Failure", "~", 0.04)`

* MaxDimensions-0 < *int* [기본값: 무제한]

    결과 패턴 당 상관 관계가 없는 차원의 최대 수를 설정 합니다. 제한을 지정 하면 쿼리 런타임이 감소 합니다.

    예제: `T | extend splitColumn = iff(request-responseCode == 200, "Success" , "Failure") | evaluate diffpatterns(splitColumn, "Success","Failure", "~", "~", 3)`

* CustomWildcard 카드- *"형식당 하나의 값"*

    현재 패턴이 이 열을 제한하지 않음을 나타내는 특정 형식에 대한 와일드카드 값을 결과 테이블에 설정합니다.
    기본값은 null이고, 문자열의 경우 기본값은 빈 문자열입니다. 기본값이 데이터에서 실행 가능한 값 이면 다른 와일드 카드 값 (예:)을 사용 해야 `*` 합니다.
    아래 예제를 참조하세요.

    예: `T | extend splitColumn = iff(request-responseCode == 200, "Success" , "Failure") | evaluate diffpatterns(splitColumn, "Success","Failure", "~", "~", "~", int(-1), double(-1), long(0), datetime(1900-1-1))`

## <a name="returns"></a>반환

`Diffpatterns`두 집합에 있는 데이터의 서로 다른 부분 (즉, 첫 번째 데이터 집합에서 높은 비율의 행을 캡처하고 두 번째 집합에 있는 행의 비율이 낮은 패턴)을 캡처하는 작은 패턴 집합을 반환 합니다. 각 패턴은 결과의 행으로 표시됩니다.

결과는 `diffpatterns` 다음 열을 반환 합니다.

* SegmentId: 현재 쿼리의 패턴에 할당 된 id입니다. 참고: Id는 반복 되는 쿼리에서 동일 하지 않을 수 있습니다.

* CountA: 집합 A의 패턴에 의해 캡처된 행의 수입니다. Set A는에 해당 합니다 `where tostring(splitColumn) == SplitValueA` .

* CountB: 집합 B의 패턴에 의해 캡처된 행의 수입니다. 집합 B는와 동일 합니다 `where tostring(splitColumn) == SplitValueB` .

* PercentA: 패턴에 의해 캡처되는 집합 A의 행 백분율 (100.0 * CountA/count (필요로)).

* PercentB: 패턴에 의해 캡처된 집합 B의 행 백분율입니다 (100.0 * CountB/count (SetB)).

* PercentDiffAB: A와 B 사이의 절대 백분율 점 차이 (| PercentA-PercentB |) 는 두 집합 간의 차이를 설명 하는 패턴의 중요 한 측정값입니다.

* 열의 나머지 부분: 입력의 원래 스키마 이며 패턴을 설명 합니다. 각 행 (패턴)은 열에 대 한 비 와일드 카드 값의 교집합을 나타냅니다 ( `where col1==val1 and col2==val2 and ... colN=valN` 행의 와일드 카드 값이 아닌 각 값에 해당).

각 패턴에 대해 패턴에 설정 되지 않은 열 (특정 값에 대 한 제한 없음)에는 기본적으로 null 인 와일드 카드 값이 포함 됩니다. 와일드 카드를 수동으로 변경 하는 방법은 아래의 인수 섹션을 참조 하십시오.

* 참고: 패턴은 종종 고유 하지 않습니다. 이러한 작업은 겹칠 수 있으며 일반적으로 모든 원본 행을 포함 하지 않습니다. 일부 행은 어떤 패턴에도 속하지 않을 수 있습니다.


**팁**

입력 파이프에 [where](./whereoperator.md) 와 [project](./projectoperator.md) 를 사용 하 여 데이터를 원하는 것 으로만 줄입니다.

관심 있는 행을 찾으면 `where` 필터에 특정 값을 추가하여 추가로 드릴인투할 수 있습니다.

* 참고: `diffpatterns` 집합 간 데이터 차이의 일부를 캡처하는 중요 한 패턴을 찾기 위해 행 단위 차이점은 아닙니다.

## <a name="example"></a>예제

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents 
| where monthofyear(StartTime) == 5
| extend Damage = iff(DamageCrops + DamageProperty > 0 , 1 , 0)
| project State , EventType , Source , Damage, DamageCrops
| evaluate diffpatterns(Damage, "0", "1" )
```

|SegmentId|CountA|CountB|PercentA|PercentB|PercentDiffAB|시스템 상태|EventType|원본|DamageCrops|
|---|---|---|---|---|---|---|---|---|---|
|0|2278|93|49.8|7.1|42.7||우박||0|
|1|779|512|17.03|39.08|22.05||뇌우를 동반한 바람|||
|2|1098|118|24.01|9.01|15|||숙련된 관찰자|0|
|3|136|158|2.97|12.06|9.09|||신문||
|4|359|214|7.85|16.34|8.49||갑작스러운 홍수|||
|5|50|122|1.09|9.31|8.22|아이오와||||
|6|655|279|14.32|21.3|6.98|||사법 기관||
|7|150|117|3.28|8.93|5.65||홍수|||
|8|362|176|7.91|13.44|5.52|||긴급 상황 관리자||
