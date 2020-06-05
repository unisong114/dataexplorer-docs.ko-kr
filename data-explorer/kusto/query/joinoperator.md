---
title: 조인 연산자-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 조인 연산자에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/30/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 2961f27226175fe81b7d9c82a6366b36134d805f
ms.sourcegitcommit: 31af2dfa75b5a2f59113611cf6faba0b45d29eb5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/05/2020
ms.locfileid: "84454119"
---
# <a name="join-operator"></a>join 연산자

두 테이블의 행을 병합하여 각 테이블에서 지정된 열의 값을 일치시키는 방식으로 새 테이블을 만듭니다.

```kusto
Table1 | join (Table2) on CommonColumn, $left.Col1 == $right.Col2
```

**구문**

*왼쪽 테이블* `|` `join`[*Joinparameters*] `(` *righttable* `)` `on` *특성*

**인수**

* 왼쪽 *테이블*: 행을 병합할 **왼쪽** 테이블 또는 테이블 형식 식 ( **외부** 테이블이 라고도 함)입니다. 로 표시 `$left` 됩니다.

* *Righttable*: 행을 병합할 **오른쪽** 테이블 또는 테이블 형식 식입니다 (**inner* table이 라고도 함). 로 표시 `$right` 됩니다.

* *특성*: *왼쪽 테이블* 의 행이 *righttable*의 행과 일치 하는 방식을 설명 하는 하나 이상의 (쉼표로 구분 된) 규칙입니다. 논리 연산자를 사용 하 여 여러 규칙을 평가 `and` 합니다.
  규칙은 다음 중 하나일 수 있습니다.

  |규칙 종류        |구문                                          |Predicate                                                      |
  |-----------------|------------------------------------------------|---------------------------------------------------------------|
  |이름으로 같음 |*ColumnName*                                    |`where`*왼쪽 테이블*. *ColumnName* `==` *Righttable*. *ColumnName*|
  |값으로 같음|`$left.`*왼쪽 열* `==` `$right.` *Rightcolumn*|`where``$left.` *왼쪽* `==` 열 `$right.` *Rightcolumn*       |

> [!NOTE]
> ' 값이 같음 ' 인 경우 *열 이름은* 및 표기법으로 표시 된 해당 소유자 테이블로 한정 되어야 합니다 `$left` `$right` .

* *Joinparameters*: *Name* `=` 행 일치 작업 및 실행 계획의 동작을 제어 하는 이름 *값* 형식의 0 개 이상의 (공백으로 구분 된) 매개 변수입니다. 지원 되는 매개 변수는 다음과 같습니다. 

::: zone pivot="azuredataexplorer"

  |속성           |값                                        |Description                                  |
  |---------------|----------------------------------------------|---------------------------------------------|
  |`kind`         |조인의 다양한 특징|[조인 특색](#join-flavors) 참조|                                             |
  |`hint.remote`  |`auto`, `left`, `local`, `right`              |[클러스터 간 조인](joincrosscluster.md) 참조|
  |`hint.strategy`|실행 힌트                               |[조인 힌트](#join-hints) 참조                |

::: zone-end

::: zone pivot="azuremonitor"

  |속성           |값                                        |Description                                  |
  |---------------|----------------------------------------------|---------------------------------------------|
  |`kind`         |조인의 다양한 특징|[조인 특색](#join-flavors) 참조|                                             |
  |`hint.remote`  |`auto`, `left`, `local`, `right`              |                                             |
  |`hint.strategy`|실행 힌트                               |[조인 힌트](#join-hints) 참조                |

::: zone-end


> [!WARNING]
> 가 지정 되지 않은 경우 기본 조인 버전은 `kind` `innerunique` 입니다. 이는 기본 버전으로가 있는 일부 다른 분석 제품과는 다릅니다 `inner` . 다른 종류 간의 차이점을 이해 하 고 쿼리가 의도 한 결과를 생성 하는지 확인 하려면 [아래](#join-flavors) 에서 주의 해 서 읽어 보세요.

**반환**

출력 스키마는 조인 버전에 따라 달라 집니다.

 * `kind=leftanti`, `kind=leftsemi`:

     결과 테이블은 좌 변에 있는 열만 포함 합니다.

     
 * `kind=rightanti`, `kind=rightsemi`:

     결과 테이블에는 우변에 있는 열만 포함 됩니다.

     
*  `kind=innerunique`, `kind=inner`, `kind=leftouter`, `kind=rightouter`, `kind=fullouter`

     일치하는 키를 포함하여 두 테이블 각각의 모든 열에 대한 열. 이름 충돌이 있는 경우 우변의 열 이름이 자동으로 바뀝니다.

     
출력 레코드는 조인 버전에 따라 달라 집니다.

 * `kind=leftanti`, `kind=leftantisemi`

     오른쪽에서 일치 하는 항목이 없는 왼쪽의 모든 레코드를 반환 합니다.     
     
 * `kind=rightanti`, `kind=rightantisemi`

     왼쪽에서 일치 하는 항목이 없는 우변의 모든 레코드를 반환 합니다.  
      
*  `kind=innerunique`, `kind=inner`, `kind=leftouter`, `kind=rightouter`, `kind=fullouter`, `kind=leftsemi`, `kind=rightsemi`

    입력된 테이블 간의 모든 일치 항목에 대한 행. 일치 항목은 `on` 다음 제약 조건을 사용 하 여 다른 테이블의 행으로 모든 필드에 대해 동일한 값을 가진 한 테이블에서 선택 된 행입니다.

   - `kind`지정 되지 않은`kind=innerunique`

    `on` 키의 각 값에 대해 좌변의 한 개의 행만 일치합니다. 출력은 오른쪽부터 이 행과 행 일치 각각에 대해 한 개의 행을 포함하고 있습니다.
    
   - `kind=leftsemi`
   
    오른쪽에서 일치 하는 항목이 있는 왼쪽의 모든 레코드를 반환 합니다.
    
   - `kind=rightsemi`
   
       왼쪽에서 일치 하는 항목을 포함 하는 오른쪽의 모든 레코드를 반환 합니다.

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

[이 예제에 대 한 자세한 정보](./samples.md#get-sessions-from-start-and-stop-events).

## <a name="join-flavors"></a>조인의 다양한 특징

join 연산자의 정확한 특징은 kind 키워드를 사용하여 지정됩니다. 현재, Kusto는 다음과 같은 조인 연산자를 지원 합니다. 

|조인 종류|Description|
|--|--|
|[`innerunique`](#default-join-flavor)(또는 기본값으로 비어 있음)|왼쪽 중복 제거를 사용 하는 내부 조인|
|[`inner`](#inner-join)|표준 내부 조인|
|[`leftouter`](#left-outer-join)|왼쪽 우선 외부 조인|
|[`rightouter`](#right-outer-join)|오른쪽 우선 외부 조인|
|[`fullouter`](#full-outer-join)|완전 외부 조인|
|[`leftanti`](#left-anti-join), [`anti`](#left-anti-join) 또는[`leftantisemi`](#left-anti-join)|왼쪽 앤티 조인|
|[`rightanti`](#right-anti-join)디스크나[`rightantisemi`](#right-anti-join)|오른쪽 앤티 조인|
|[`leftsemi`](#left-semi-join)|왼쪽 세미 조인|
|[`rightsemi`](#right-semi-join)|오른쪽 세미 조인|

### <a name="inner-and-innerunique-join-operator-flavors"></a>inner 및 innerunique 조인 연산자 특색

-    **내부 조인 버전**을 사용 하는 경우 left 키가 없는 left 및 right와 일치 하는 행의 모든 조합에 대해 출력에 행이 deduplications 됩니다. 출력은 왼쪽 및 오른쪽 키의 데카르트 곱입니다.
    **내부 조인의**예:

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

|key|값|key1|value1|
|---|---|---|---|
|1|val 1.2|1|val 1.3|
|1|val 1.1|1|val 1.3|
|1|val 1.2|1|val 1.4|
|1|val 1.1|1|val 1.4|

-    **Innerunique 조인 버전** 을 사용 하면 왼쪽의 키가 중복 제거 되며 중복 제거 된 왼쪽 키와 오른쪽 키의 모든 조합에서 출력에 행이 있습니다.
    위에서 사용 되는 것과 동일한 데이터 집합에 대 한 **innerunique 조인의** 예를 사용 하는 경우이 사례에 대 한 **innerunique 버전** 은 가능한 두 개의 출력을 생성할 수 있으며 둘 다 올바릅니다.
    첫 번째 출력에서 조인 연산자는 "val 1.1" 값을 사용 하 여 t 1에 표시 되 고 t2 키와 일치 하는 첫 번째 키를 임의로 선택 하 여 두 번째 키에 있는 반면, 조인 연산자는 값 "val 1.2"를 포함 하 고 t2 키와 일치 하는 t 1에 두 번째 키를 임의로 선택 합니다.

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

|key|값|key1|value1|
|---|---|---|---|
|1|val 1.1|1|val 1.3|
|1|val 1.1|1|val 1.4|

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

|key|값|key1|value1|
|---|---|---|---|
|1|val 1.2|1|val 1.3|
|1|val 1.2|1|val 1.4|


-    Kusto는가 `join` 가능한 경우 왼쪽 또는 오른쪽에 있는 적절 한 조인 측면을 향해 오는 필터를 푸시하는 데 최적화 되어 있습니다.
    경우에 따라 사용 되는 버전이 **innerunique** 이 고 필터를 조인의 왼쪽으로 전파할 수 있는 경우 자동으로 전파 되 고 해당 필터에 적용 되는 키는 항상 출력에 표시 됩니다.
    예를 들어 위의 예제를 사용 하 여 필터를 추가 ` where value == "val1.2" ` 하면 항상 두 번째 결과를 제공 하 고 사용 된 데이터 집합에 대 한 첫 번째 결과를 제공 하지 않습니다.

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

|key|값|key1|value1|
|---|---|---|---|
|1|val 1.2|1|val 1.3|
|1|val 1.2|1|val 1.4|
 
### <a name="default-join-flavor"></a>기본 조인 버전
    
    X | join Y on Key
    X | join kind=innerunique Y on Key
     
조인 작업을 설명 하는 두 개의 샘플 테이블을 사용 하겠습니다. 
 
테이블 X 

|키 |값1 
|---|---
|a |1 
|b |2 
|b |3 
|c |4 

테이블 Y 

|키 |값2 
|---|---
|b |10 
|c |20 
|c |30 
|일 |40 
 
기본 조인은 조인 키의 왼쪽에서 중복 제거 뒤에서 내부 조인을 수행합니다(중복 제거에서 첫 번째 레코드는 보존됨). 이 문을 가정합니다. 

    X | join Y on Key 

조인의 효과적인 왼쪽(중복 제거 후 테이블 X)은 다음과 같습니다. 

|키 |값1 
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

|키|값1|Key1|값2|
|---|---|---|---|
|b|2|b|10|
|c|4|c|20|
|c|4|c|30|


왼쪽 및 오른쪽 모두에 일치 하는 키가 없기 때문에 ' a ' 및 ' d ' 키는 출력에 표시 되지 않습니다. 
 
(이전에는 Kusto의 초기 버전에서 지원 되는 조인의 첫 번째 구현입니다.) 일반적인 로그/추적 분석 시나리오에서는 동일한 상관 관계 ID에서 두 이벤트 (각 필터링 조건과 일치 하는 각 이벤트)의 상관 관계를 지정할 수 있으며, 영향을 받는 추적 레코드의 여러 모양을 무시 하 고 찾고 있는 모든 현상의 모든 모양새를 다시 가져올 수 있습니다.
 
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

|키|값1|Key1|값2|
|---|---|---|---|
|b|3|b|10|
|b|2|b|10|
|c|4|c|20|
|c|4|c|30|

오른쪽에서 오는 (b, 10)은 두 번 조인 됩니다. (b, 2) 및 (b, 3)는 왼쪽에 있습니다. 또한 왼쪽의 (c, 4)가 두 번 조인 되었습니다. (c, 20) 및 (c, 30)이 오른쪽에 있습니다. 

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

|키|값1|Key1|값2|
|---|---|---|---|
|b|3|b|10|
|b|2|b|10|
|c|4|c|20|
|c|4|c|30|
|a|1|||

 
### <a name="right-outer-join"></a>오른쪽 우선 외부 조인 

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

|키|값1|Key1|값2|
|---|---|---|---|
|b|3|b|10|
|b|2|b|10|
|c|4|c|20|
|c|4|c|30|
|||일|40|

 
### <a name="full-outer-join"></a>완전 외부 조인 

개념적으로 볼 때 전체 외부 조인은 왼쪽과 오른쪽 조인을 모두 적용한 효과를 결합합니다. 조인 된 테이블의 레코드가 일치 하지 않는 경우 결과 집합은 일치 하는 행이 없는 테이블의 모든 열에 대해 NULL 값을 갖습니다. 일치하는 레코드의 경우 결과 집합(두 테이블에서 나온 내용으로 채워진 필드를 포함함)에 단일 행이 생성됩니다. 
 
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

|키|값1|Key1|값2|
|---|---|---|---|
|b|3|b|10|
|b|2|b|10|
|c|4|c|20|
|c|4|c|30|
|||일|40|
|a|1|||

 
### <a name="left-anti-join"></a>왼쪽 앤티 조인

왼쪽 앤티 조인은 오른쪽의 레코드와 일치 하지 않는 왼쪽의 모든 레코드를 반환 합니다. 
 
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

|키|값1|
|---|---|
|a|1|

앤티 조인은 “NOT IN” 쿼리를 모델링합니다. 

### <a name="right-anti-join"></a>오른쪽 앤티 조인

오른쪽 앤티 조인은 좌 변에 있는 레코드와 일치 하지 않는 오른쪽의 모든 레코드를 반환 합니다. 
 
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

|키|값2|
|---|---|
|일|40|

앤티 조인은 “NOT IN” 쿼리를 모델링합니다. 

### <a name="left-semi-join"></a>왼쪽 세미 조인

왼쪽 세미 조인은 우변에서 레코드와 일치 하는 모든 레코드를 왼쪽에서 반환 합니다. 좌 변의 열만 반환 됩니다. 

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

|키|값1|
|---|---|
|b|3|
|b|2|
|c|4|

### <a name="right-semi-join"></a>오른쪽 세미 조인

오른쪽 세미 조인은 좌 변에 있는 레코드와 일치 하는 오른쪽의 모든 레코드를 반환 합니다. 우변의 열만 반환 됩니다. 

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

|키|값2|
|---|---|
|b|10|
|c|20|
|c|30|


### <a name="cross-join"></a>크로스 조인

Kusto는 기본적으로 크로스 조인 버전을 제공 하지 않습니다 (즉, 연산자를로 표시할 수 없음 `kind=cross` ).
그러나 더미 키를 사용 하 여이를 시뮬레이션 하는 것은 어렵습니다.

    X | extend dummy=1 | join kind=inner (Y | extend dummy=1) on dummy

## <a name="join-hints"></a>조인 힌트

`join`연산자는 쿼리가 실행 되는 방식을 제어 하는 여러 힌트를 지원 합니다.
이는의 의미 체계가 변경 되지 `join` 않지만 성능에 영향을 줄 수 있습니다.

조인 힌트는 다음 문서에서 설명 합니다. 
* `hint.shufflekey=<key>``hint.strategy=shuffle`  -  [쿼리 순서 섞기](shufflequery.md)
* `hint.strategy=broadcast` - [브로드캐스트 조인](broadcastjoin.md)
* `hint.remote=<strategy>` - [클러스터 간 조인](joincrosscluster.md)
