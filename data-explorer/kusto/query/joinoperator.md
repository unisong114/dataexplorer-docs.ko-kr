---
title: 조인 연산자 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기에서 조인 연산자에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/30/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 86d883c8d0214d278099260fa2406b91b776b4d1
ms.sourcegitcommit: 01eb9aaf1df2ebd5002eb7ea7367a9ef85dc4f5d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81765821"
---
# <a name="join-operator"></a>join 연산자

두 테이블의 행을 병합하여 각 테이블에서 지정된 열의 값을 일치시키는 방식으로 새 테이블을 만듭니다.

```kusto
Table1 | join (Table2) on CommonColumn, $left.Col1 == $right.Col2
```

**구문**

*왼쪽 테이블* `|` `join` [*조인 매개 변수*] `(` 오른쪽 *테이블* `)` `on` *특성*

**인수**

* *LeftTable*: 행을 병합할 **왼쪽** 테이블 또는 테이블 형식식(외부 테이블이라고도 함). **outer** `$left`로 표시됩니다.

* *RightTable:* 행을 병합할 **오른쪽** 테이블 또는 테이블 형식 식(*내부 테이블이라고도 함).*inner* `$right`로 표시됩니다.

* *특성*: *LeftTable의* 행이 *RightTable의*행과 일치하는 방법을 설명하는 하나 이상의 (쉼표로 구분된) 규칙 . 논리 연산자를 `and` 사용하여 여러 규칙을 평가합니다.
  규칙은 다음 중 하나일 수 있습니다.

  |규칙 종류        |구문                                          |Predicate                                                      |
  |-----------------|------------------------------------------------|---------------------------------------------------------------|
  |이름으로 의한 같음 |*ColumnName*                                    |`where`*왼쪽 테이블*. *열 이름* `==` *오른쪽 테이블*. *열 이름*|
  |값별 같음|`$left.`*왼쪽열* `==` `$right.` *오른쪽 열*|`where``$left.` *LeftColumn* `==` 왼쪽열 `$right.` *오른쪽 열*       |

> [!NOTE]
> '값별 같음'의 경우 열 이름은 표기명으로 표시된 해당 `$left` 소유자 `$right` 테이블과 함께 *정규화되어야 합니다.*

* *JoinParameters*: 행 일치 작업 및 실행 계획의 동작을 제어하는 *이름* `=` *값* 의 형태로 0 개 이상의 (공간 구분) 매개 변수입니다. 다음 매개 변수가 지원됩니다. 

::: zone pivot="azuredataexplorer"

  |이름           |값                                        |Description                                  |
  |---------------|----------------------------------------------|---------------------------------------------|
  |`kind`         |조인의 다양한 특징|[조인 맛](#join-flavors) 보기|                                             |
  |`hint.remote`  |`auto`, `left`, `local`, `right`              |[클러스터 간 조인](joincrosscluster.md) 참조|
  |`hint.strategy`|실행 힌트                               |[힌트 조인](#join-hints) 참조                |

::: zone-end

::: zone pivot="azuremonitor"

  |이름           |값                                        |Description                                  |
  |---------------|----------------------------------------------|---------------------------------------------|
  |`kind`         |조인의 다양한 특징|[조인 맛](#join-flavors) 보기|                                             |
  |`hint.remote`  |`auto`, `left`, `local`, `right`              |                                             |
  |`hint.strategy`|실행 힌트                               |[힌트 조인](#join-hints) 참조                |

::: zone-end


> [!WARNING]
> 기본 조인 맛(지정되지 않은 `innerunique`경우)은 `kind` 입니다. 이는 기본 맛이 있는 `inner` 다른 분석 제품과는 다릅니다. [아래를](#join-flavors) 주의 깊게 읽으시면 서로 다른 종류 간의 차이점을 이해하고 쿼리가 의도한 결과를 산출하는지 확인하십시오.

**반환**

출력 스키마는 조인 맛에 따라 달라집니다.

 * `kind=leftanti`, `kind=leftsemi`:

     결과 테이블에는 왼쪽의 열만 포함됩니다.

     
 * `kind=rightanti`, `kind=rightsemi`:

     결과 테이블에는 오른쪽의 열만 포함됩니다.

     
*  `kind=innerunique`, `kind=inner`, `kind=leftouter`, `kind=rightouter`, `kind=fullouter`

     일치하는 키를 포함하여 두 테이블 각각의 모든 열에 대한 열. 이름 충돌이 있는 경우 우변의 열 이름이 자동으로 바뀝니다.

     
출력 레코드는 조인 맛에 따라 다릅니다.

 * `kind=leftanti`, `kind=leftantisemi`

     오른쪽에서 일치하지 않는 왼쪽의 모든 레코드를 반환합니다.     
     
 * `kind=rightanti`, `kind=rightantisemi`

     왼쪽에서 일치하지 않는 오른쪽에서 모든 레코드를 반환합니다.  
      
*  `kind=innerunique`, `kind=inner`, `kind=leftouter`, `kind=rightouter`, `kind=fullouter`, `kind=leftsemi`, `kind=rightsemi`

    입력된 테이블 간의 모든 일치 항목에 대한 행. 일치는 다음 제약 조건을 가진 다른 테이블의 `on` 행과 동일한 값을 가진 한 테이블에서 선택된 행입니다.

   - `kind`불특정`kind=innerunique`

    `on` 키의 각 값에 대해 좌변의 한 개의 행만 일치합니다. 출력은 오른쪽부터 이 행과 행 일치 각각에 대해 한 개의 행을 포함하고 있습니다.
    
   - `kind=leftsemi`
   
    오른쪽에서 일치하는 왼쪽의 모든 레코드를 반환합니다.
    
   - `kind=rightsemi`
   
       왼쪽에서 일치하는 오른쪽에서 모든 레코드를 반환합니다.

   - `kind=inner`
 
    왼쪽 및 오른쪽에서 일치하는 행의 모든 조합에 대해 한 개의 출력 행이 있습니다.

   - `kind=leftouter`(또는 `kind=rightouter` 또는 `kind=fullouter`)

    내부 일치 외에도 일치 항목이 없더라도 왼쪽(및/또는 오른쪽)의 모든 행에 대한 행이 있습니다. 이 경우 일치하지 않는 출력 셀에는 null 값이 포함되어 있습니다.
    이러한 필드에 대해 같은 값을 가진 여러 행이 있는 경우 모든 조합에 대한 행을 얻습니다.

 

**팁**

최상의 성능을 얻으려면:

* 한 테이블이 언제나 다른 테이블보다 더 작으면 해당 테이블을 조인의 좌변(파이프된)으로 사용합니다.

**예제**

로그에서 일부 항목이 활동의 시작과 끝을 표시하는 확장된 활동을 가져옵니다. 

```kusto
let Events = MyLogTable | where type=="Event" ;
Events
| where Name == "Start"
| project Name, City, ActivityId, StartTime=timestamp
| join (Events
    | where Name == "Stop"
        | project StopTime=timestamp, ActivityId)
    on ActivityId
| project City, ActivityId, StartTime, StopTime, Duration = StopTime - StartTime
```

```kusto
let Events = MyLogTable | where type=="Event" ;
Events
| where Name == "Start"
| project Name, City, ActivityIdLeft = ActivityId, StartTime=timestamp
| join (Events
        | where Name == "Stop"
        | project StopTime=timestamp, ActivityIdRight = ActivityId)
    on $left.ActivityIdLeft == $right.ActivityIdRight
| project City, ActivityId, StartTime, StopTime, Duration = StopTime - StartTime
```

[이 예제에 대한 자세한 내용은](./samples.md#activities).

## <a name="join-flavors"></a>조인의 다양한 특징

join 연산자의 정확한 특징은 kind 키워드를 사용하여 지정됩니다. 오늘 현재 Kusto는 조인 연산자의 다음과 같은 맛을 지원합니다. 

|조인 종류|Description|
|--|--|
|[`innerunique`](#default-join-flavor)(또는 기본값으로 비어 있음)|왼쪽 중복 제거가 있는 내부 조인|
|[`inner`](#inner-join)|표준 이너 조인|
|[`leftouter`](#left-outer-join)|왼쪽 우선 외부 조인|
|[`rightouter`](#right-outer-join)|오른쪽 외부 조인|
|[`fullouter`](#full-outer-join)|전체 외부 조인|
|[`leftanti`](#left-anti-join)을, [`anti`](#left-anti-join) 또는[`leftantisemi`](#left-anti-join)|왼쪽 안티 조인|
|[`rightanti`](#right-anti-join)또는[`rightantisemi`](#right-anti-join)|오른쪽 안티 조인|
|[`leftsemi`](#left-semi-join)|왼쪽 세미 조인|
|[`rightsemi`](#right-semi-join)|오른쪽 세미 조인|

### <a name="inner-and-innerunique-join-operator-flavors"></a>내부 및 내부 고유 조인 연산자 맛

-    내부 **조인 맛을**사용하는 경우 왼쪽 키 중복 제거 없이 왼쪽과 오른쪽에서 일치하는 행의 모든 조합에 대해 출력에 행이 표시됩니다. 출력은 왼쪽 및 오른쪽 키의 카르테시안 제품이 됩니다.
    내부 **조인의**예 :

```kusto
let t1 = datatable(key:long, value:string)  
[
1, "val1.1",  
1, "val1.2"  
];
let t2 = datatable(key:long, value:string)  
[  
1, "val1.3", 
1, "val1.4"  
];
t1
| join kind = inner
    t2
on key
```

|key|value|key1|value1|
|---|---|---|---|
|1|val1.2|1|val1.3|
|1|val1.1|1|val1.3|
|1|val1.2|1|val1.4|
|1|val1.1|1|val1.4|

-    **innerunique 조인 맛을** 사용하면 왼쪽에서 키가 중복제거되고 중복 제거된 왼쪽 키와 오른쪽 키의 모든 조합에서 출력에 행이 표시됩니다.
    위에서 사용한 동일한 데이터 집합에 대한 **innerunique 조인의** 예는 이 경우 **내부 고유 풍미가** 두 개의 가능한 출력을 생성할 수 있으며 둘 다 정확하다는 점에 유의하십시오.
    첫 번째 출력에서 조인 연산자는 값 "val1.1"으로 t1에 나타나는 첫 번째 키를 임의로 골라 t2 키와 일치시켰으며, 두 번째 키에서는 두 번째 키가 "val1.2"라는 값을 가진 t1에 임의로 표시되고 t2 키와 일치합니다.

```kusto
let t1 = datatable(key:long, value:string)  
[
1, "val1.1",  
1, "val1.2"  
];
let t2 = datatable(key:long, value:string)  
[  
1, "val1.3", 
1, "val1.4"  
];
t1
| join kind = innerunique
    t2
on key
```

|key|value|key1|value1|
|---|---|---|---|
|1|val1.1|1|val1.3|
|1|val1.1|1|val1.4|

```kusto
let t1 = datatable(key:long, value:string)  
[
1, "val1.1",  
1, "val1.2"  
];
let t2 = datatable(key:long, value:string)  
[  
1, "val1.3", 
1, "val1.4"  
];
t1
| join kind = innerunique
    t2
on key
```

|key|value|key1|value1|
|---|---|---|---|
|1|val1.2|1|val1.3|
|1|val1.2|1|val1.4|


-    Kusto는 가능한 경우 적절한 조인 `join` 측을 향해 오는 필터를 밀어 내도록 최적화되어 있습니다.
    경우에 따라 사용되는 맛이 **내부고유이고** 필터가 조인의 왼쪽으로 전파될 수 있는 경우 자동으로 전파되고 해당 필터에 적용되는 키가 항상 출력에 나타납니다.
    예를 들어 위의 예제를 사용하고 ` where value == "val1.2" ` 필터를 추가하면 항상 두 번째 결과가 제공되며 사용된 데이터 집합에 대한 첫 번째 결과가 발생하지 않습니다.

```kusto
let t1 = datatable(key:long, value:string)  
[
1, "val1.1",  
1, "val1.2"  
];
let t2 = datatable(key:long, value:string)  
[  
1, "val1.3", 
1, "val1.4"  
];
t1
| join kind = innerunique
    t2
on key
| where value == "val1.2"
```

|key|value|key1|value1|
|---|---|---|---|
|1|val1.2|1|val1.3|
|1|val1.2|1|val1.4|
 
### <a name="default-join-flavor"></a>기본 조인 맛
    
    X | join Y on Key
    X | join kind=innerunique Y on Key
     
두 개의 샘플 테이블을 사용하여 조인의 작업을 설명해 보겠습니다. 
 
테이블 X 

|Key |값1 
|---|---
|a |1 
|b |2 
|b |3 
|c |4 

테이블 Y 

|Key |값2 
|---|---
|b |10 
|c |20 
|c |30 
|d |40 
 
기본 조인은 조인 키의 왼쪽에서 중복 제거 뒤에서 내부 조인을 수행합니다(중복 제거에서 첫 번째 레코드는 보존됨). 이 문을 가정합니다. 

    X | join Y on Key 

조인의 효과적인 왼쪽(중복 제거 후 테이블 X)은 다음과 같습니다. 

|Key |값1 
|---|---
|a |1 
|b |2 
|c |4 

및 조인의 결과: 

```kusto
let X = datatable(Key:string, Value1:long)
[
    'a',1,
    'b',2,
    'b',3,
    'c',4
];
let Y = datatable(Key:string, Value2:long)
[
    'b',10,
    'c',20,
    'c',30,
    'd',40
];
X | join Y on Key
```

|Key|값1|키1|값2|
|---|---|---|---|
|b|2|b|10|
|c|4|c|20|
|c|4|c|30|


(왼쪽과 오른쪽에 일치하는 키가 없기 때문에 키 'a'와 'd'는 출력에 나타나지 않습니다. 
 
(역사적으로, 이것은 Kusto의 초기 버전에서 지원되는 조인의 첫 번째 구현이었습니다. 동일한 상관 관계 ID에서 두 이벤트 (각각 필터링 기준일치)를 상호 연관시키고 기여 추적 레코드의 여러 모양을 무시하고 찾고있는 현상의 모든 모양을 되돌리려는 일반적인 로그 / 추적 분석 시나리오에서 유용합니다.
 
### <a name="inner-join"></a>내부 조인

이는 SQL 환경에서 알려진 표준 내부 조인입니다. 출력 레코드는 왼쪽의 레코드가 오른쪽의 레코드와 같은 조인 키를 가질 때마다 생성됩니다. 
 
```kusto
let X = datatable(Key:string, Value1:long)
[
    'a',1,
    'b',2,
    'b',3,
    'c',4
];
let Y = datatable(Key:string, Value2:long)
[
    'b',10,
    'c',20,
    'c',30,
    'd',40
];
X | join kind=inner Y on Key
```

|Key|값1|키1|값2|
|---|---|---|---|
|b|3|b|10|
|b|2|b|10|
|c|4|c|20|
|c|4|c|30|

오른쪽에서 오는 (b,10) 두 번 결합했다: (b,2) 그리고 (b,3) 왼쪽에; 또한 (c,4) 왼쪽에 두 번 결합했다 : 오른쪽에 (c,20) 및 (c,30) 모두와 함께. 

### <a name="left-outer-join"></a>왼쪽 우선 외부 조인 

테이블 X 및 Y에 대한 왼쪽 우선 외부 조인의 결과는 조인 조건이 오른쪽 테이블(Y)에서 일치하는 레코드를 찾을 수 없더라도 언제나 왼쪽 테이블(X)의 모든 레코드를 포함합니다. 
 
```kusto
let X = datatable(Key:string, Value1:long)
[
    'a',1,
    'b',2,
    'b',3,
    'c',4
];
let Y = datatable(Key:string, Value2:long)
[
    'b',10,
    'c',20,
    'c',30,
    'd',40
];
X | join kind=leftouter Y on Key
```

|Key|값1|키1|값2|
|---|---|---|---|
|b|3|b|10|
|b|2|b|10|
|c|4|c|20|
|c|4|c|30|
|a|1|||

 
### <a name="right-outer-join"></a>오른쪽 외부 조인 

왼쪽 우선 외부 조인과 유사하지만 테이블에 대한 처리가 반대로 바뀝니다. 
 
```kusto
let X = datatable(Key:string, Value1:long)
[
    'a',1,
    'b',2,
    'b',3,
    'c',4
];
let Y = datatable(Key:string, Value2:long)
[
    'b',10,
    'c',20,
    'c',30,
    'd',40
];
X | join kind=rightouter Y on Key
```

|Key|값1|키1|값2|
|---|---|---|---|
|b|3|b|10|
|b|2|b|10|
|c|4|c|20|
|c|4|c|30|
|||d|40|

 
### <a name="full-outer-join"></a>전체 외부 조인 

개념적으로 볼 때 전체 외부 조인은 왼쪽과 오른쪽 조인을 모두 적용한 효과를 결합합니다. 조인된 테이블의 레코드가 일치하지 않는 경우 결과 집합에는 일치하는 행이 없는 테이블의 모든 열에 대해 NULL 값이 표시됩니다. 일치하는 레코드의 경우 결과 집합(두 테이블에서 나온 내용으로 채워진 필드를 포함함)에 단일 행이 생성됩니다. 
 
```kusto
let X = datatable(Key:string, Value1:long)
[
    'a',1,
    'b',2,
    'b',3,
    'c',4
];
let Y = datatable(Key:string, Value2:long)
[
    'b',10,
    'c',20,
    'c',30,
    'd',40
];
X | join kind=fullouter Y on Key
```

|Key|값1|키1|값2|
|---|---|---|---|
|b|3|b|10|
|b|2|b|10|
|c|4|c|20|
|c|4|c|30|
|||d|40|
|a|1|||

 
### <a name="left-anti-join"></a>왼쪽 안티 조인

왼쪽 안티 조인은 오른쪽에서 레코드와 일치하지 않는 왼쪽의 모든 레코드를 반환합니다. 
 
```kusto
let X = datatable(Key:string, Value1:long)
[
    'a',1,
    'b',2,
    'b',3,
    'c',4
];
let Y = datatable(Key:string, Value2:long)
[
    'b',10,
    'c',20,
    'c',30,
    'd',40
];
X | join kind=leftanti Y on Key
```

|Key|값1|
|---|---|
|a|1|

앤티 조인은 “NOT IN” 쿼리를 모델링합니다. 

### <a name="right-anti-join"></a>오른쪽 안티 조인

오른쪽 안티 조인은 왼쪽에서 레코드와 일치하지 않는 오른쪽의 모든 레코드를 반환합니다. 
 
```kusto
let X = datatable(Key:string, Value1:long)
[
    'a',1,
    'b',2,
    'b',3,
    'c',4
];
let Y = datatable(Key:string, Value2:long)
[
    'b',10,
    'c',20,
    'c',30,
    'd',40
];
X | join kind=rightanti Y on Key
```

|Key|값2|
|---|---|
|d|40|

앤티 조인은 “NOT IN” 쿼리를 모델링합니다. 

### <a name="left-semi-join"></a>왼쪽 세미 조인

왼쪽 세미 조인은 오른쪽에서 레코드와 일치하는 왼쪽의 모든 레코드를 반환합니다. 왼쪽의 열만 반환됩니다. 

```kusto
let X = datatable(Key:string, Value1:long)
[
    'a',1,
    'b',2,
    'b',3,
    'c',4
];
let Y = datatable(Key:string, Value2:long)
[
    'b',10,
    'c',20,
    'c',30,
    'd',40
];
X | join kind=leftsemi Y on Key
```

|Key|값1|
|---|---|
|b|3|
|b|2|
|c|4|

### <a name="right-semi-join"></a>오른쪽 세미 조인

오른쪽 세미 조인은 왼쪽에서 레코드와 일치하는 오른쪽의 모든 레코드를 반환합니다. 오른쪽의 열만 반환됩니다. 

```kusto
let X = datatable(Key:string, Value1:long)
[
    'a',1,
    'b',2,
    'b',3,
    'c',4
];
let Y = datatable(Key:string, Value2:long)
[
    'b',10,
    'c',20,
    'c',30,
    'd',40
];
X | join kind=rightsemi Y on Key
```

|Key|값2|
|---|---|
|b|10|
|c|20|
|c|30|


### <a name="cross-join"></a>교차 조인

Kusto는 기본적으로 교차 조인 맛을 제공하지 않습니다 (즉, 연산자에게 `kind=cross`표시 할 수 없습니다).
그러나 더미 키를 마련하여 이를 시뮬레이션하는 것은 어렵지 않습니다.

    X | extend dummy=1 | join kind=inner (Y | extend dummy=1) on dummy

## <a name="join-hints"></a>힌트 조인

연산자는 `join` 쿼리실행 방식을 제어하는 여러 힌트를 지원합니다.
의 의미체계를 `join`변경하지는 않지만 성능에 영향을 줄 수 있습니다.

다음 문서에 설명된 힌트 조인: 
* `hint.shufflekey=<key>`및 `hint.strategy=shuffle`  -  [셔플 쿼리](shufflequery.md)
* `hint.strategy=broadcast` - [브로드캐스트 조인](broadcastjoin.md)
* `hint.remote=<strategy>` - [클러스터 간 조인](joincrosscluster.md)
