---
title: diffpatterns 플러그인 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 diffpatterns 플러그인에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: fc4d1c7441e02eeeb1d90e1f8c0f2a521e3793da
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81516000"
---
# <a name="diffpatterns-plugin"></a>디프패턴 플러그인

```kusto
T | evaluate diffpatterns(splitColumn)
```
동일한 구조의 두 데이터 집합을 비교하고 두 데이터 집합 간의 차이를 특성화하는 불연속 특성(차원)의 패턴을 찾습니다. Diffpatterns는 오류를 분석하는 데 도움을 주기 위해 개발되었으나(예: 지정된 시간 내에 오류를 비오류와 비교) 동일한 구조의 모든 두 데이터 집합 간의 차이를 발견할 수도 있습니다. 

**구문**

`T | evaluate diffpatterns(`분할열, 분할값A, 분할값B [, 가중치, 임계값, 최대치수, 사용자 정의 와일드 카드, ...]`)` 

**필수 인수**

* SplitColumn - *column_name*

    알고리즘에 쿼리를 데이터 집합으로 분할하는 방법을 알려줍니다. SplitValueA 및 SplitValueB 인수(아래 참조)에 지정된 값에 따라 알고리즘은 쿼리를 두 개의 데이터 집합 “A” 및 “B”로 분할하고 둘 사이의 차이점을 분석합니다. 따라서 분할 열은 둘 이상의 고유 값을 가져야 합니다.

* SplitValueA - *string*

    지정된 SplitColumn에 있는 값 중 하나를 문자열로 나타낸 것입니다. 해당 SplitColumn에 이 값이 있는 모든 행은 데이터 집합 “A”로 간주됩니다.

* SplitValueB - *string*

    지정된 SplitColumn에 있는 값 중 하나를 문자열로 나타낸 것입니다. SplitColumn에서 이 값을 가진 모든 행은 데이터 집합 "B"로 간주됩니다.

    예: `T | extend splitColumn=iff(request_responseCode == 200, "Success" , "Failure") | evaluate diffpatterns(splitColumn, "Success","Failure") `

**선택적 인수**

다른 모든 인수는 선택 사항이지만, 아래와 같은 순서여야 합니다. 기본값을 사용해야 함을 나타내려면 문자열 물결표 값 '~'을 사용합니다(아래 예 참조).

* WeightColumn - *column_name*

    지정된 가중치(기본적으로 각 행의 가중치는 '1'임)에 따라 입력의 각 행을 고려합니다. 이 인수는 숫자 열의 이름이어야 합니다(예: int, long, real).
    이미 각 행에 포함되어 있는 데이터의 계정 샘플링 또는 버킷팅/집계가 가중치 열의 일반적인 용도로 간주됩니다.
    
    예: `T | extend splitColumn=iff(request_responseCode == 200, "Success" , "Failure") | evaluate diffpatterns(splitColumn, "Success","Failure", sample_Count) `

* 임계값 - 0.015 < *더블* < 1 [기본값: 0.05]

    두 집합 간의 최소 패턴(비율) 차이를 설정합니다.

    예제: `T | extend splitColumn = iff(request-responseCode == 200, "Success" , "Failure") | evaluate diffpatterns(splitColumn, "Success","Failure", "~", 0.04)`

* 최대치수 - 0 < *int* [기본값: 무제한]

    결과 패턴당 상관 관계가 없는 차원의 최대 수를 설정하여 제한을 지정함으로써 쿼리 런타임을 줄입니다.

    예제: `T | extend splitColumn = iff(request-responseCode == 200, "Success" , "Failure") | evaluate diffpatterns(splitColumn, "Success","Failure", "~", "~", 3)`

* 사용자 정의 와일드 카드 - *"유형별 모든 값"*

    현재 패턴이 이 열을 제한하지 않음을 나타내는 특정 형식에 대한 와일드카드 값을 결과 테이블에 설정합니다.
    기본값은 null이고, 문자열의 경우 기본값은 빈 문자열입니다. 기본값이 데이터에서 실행 가능한 값인 경우 다른 와일드카드 값(예: `*`)을 사용해야 합니다.
    아래 예제를 참조하세요.

    예: `T | extend splitColumn = iff(request-responseCode == 200, "Success" , "Failure") | evaluate diffpatterns(splitColumn, "Success","Failure", "~", "~", "~", int(-1), double(-1), long(0), datetime(1900-1-1))`

**반환**

Diffpatterns는 두 집합에 있는 데이터의 서로 다른 부분을 캡처하는 패턴 (일반적으로 작은)(예: 첫 번째 데이터 집합에서는 높은 비율의 행을 캡처하고, 두 번째 집합에서는 낮은 비율의 행을 캡처하는 패턴) 집합을 반환합니다. 각 패턴은 결과의 행으로 표시됩니다.

diffpatterns의 결과는 다음 열을 반환합니다.

* SegmentId: 현재 쿼리의 패턴에 할당된 ID입니다(참고: ID는 반복 쿼리에서 동일하지 않음).

* CountA: 세트 A의 패턴에 의해 캡처된 행 수(세트 A는 `where tostring(splitColumn) == SplitValueA`동일)입니다.

* CountB: 집합 B의 패턴에 의해 캡처된 행 수(세트 B는 `where tostring(splitColumn) == SplitValueB`동일)입니다.

* 백분율A: 패턴에 의해 캡처된 Set A의 행 백분율(100.0 * CountA / CountA(SetA))입니다.

* 백분율B: 패턴으로 캡처한 집합 B의 행 백분율(100.0 * CountB / CountB(SetB)))

* 백분율DiffAB: A와 B 사이의 절대 백분율 포인트 차이 (| 퍼센트A - 퍼센트B| )는 두 세트의 차이를 설명할 때 패턴의 중요성을 측정하는 주요 척도입니다.

* 나머지 열: 입력의 원래 스키마이며 패턴을 설명하고 각 행(패턴)은 열의 와일드카드가 아닌 값의 교차를 다시 합니다(행의 각 와일드카드 가액에 해당). `where col1==val1 and col2==val2 and ... colN=valN`

각 패턴에 대해 패턴에 설정되지 않은 열(예: 특정 값에 대한 제한 없이)에는 기본적으로 null인 와일드카드 값이 포함됩니다(와일드카드를 수동으로 변경할 수 있는 방법 아래의 인수 섹션 참조).

* 참고: 패턴은 일반적으로 구별되지 않으며 겹칠 수 있으며 일반적으로 모든 원래 행을 다루지 않습니다. 일부 행은 어떤 패턴에도 속하지 않을 수 있습니다.


**팁**

입력 파이프의 [위치와 투영을](./whereoperator.md) 사용하여 관심 있는 데이터로 데이터를 줄입니다. [project](./projectoperator.md)

관심 있는 행을 찾으면 `where` 필터에 특정 값을 추가하여 추가로 드릴인투할 수 있습니다.

* 참고: diffpatterns는 집합 간의 데이터 차이의 일부를 캡처하는 중요한 패턴(집합 간의 데이터 차이 의 일부를 캡처)을 찾는 것을 목표로 하며 행별 차이를 의미하지는 않습니다.

**예제**

```kusto
StormEvents 
| where monthofyear(StartTime) == 5
| extend Damage = iff(DamageCrops + DamageProperty > 0 , 1 , 0)
| project State , EventType , Source , Damage, DamageCrops
| evaluate diffpatterns(Damage, "0", "1" )
```
|SegmentId|CountA|CountB|PercentA|PercentB|퍼센트디파브|시스템 상태|EventType|원본|DamageCrops|
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

