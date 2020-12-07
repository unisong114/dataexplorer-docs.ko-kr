---
title: join 연산자 - Azure Data Explorer
description: 이 문서에서는 Azure Data Explorer의 join 연산자에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 03/30/2020
ms.localizationpriority: high
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: b90e5f1c95ec75a946490cd75b5dd89ad2cb1aba
ms.sourcegitcommit: f49e581d9156e57459bc69c94838d886c166449e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2020
ms.locfileid: "95513338"
---
# <a name="join-operator"></a>join 연산자

두 테이블의 행을 병합하여 각 테이블에서 지정된 열의 값을 일치시키는 방식으로 새 테이블을 만듭니다.

```kusto
Table1 | join (Table2) on CommonColumn, $left.Col1 == $right.Col2
```

## <a name="syntax"></a>Syntax

*LeftTable* `|` `join` [*JoinParameters*] `(` *RightTable* `)` `on` *Attributes*

## <a name="arguments"></a>인수

* *LeftTable*: **왼쪽** 테이블 또는 테이블 형식 식이며, 행을 병합할 **외부** 테이블이라고도 합니다. `$left`로 표시됩니다.

* *RightTable*: **오른쪽**  테이블 또는 테이블 형식 식이며, 행을 병합할 **내부** 테이블이라고도 합니다. `$right`로 표시됩니다.

* *Attributes*: *LeftTable* 의 행을 *RightTable* 의 행과 일치시키는 방법을 설명하는 하나 이상의 쉼표로 구분된 **규칙** 입니다. 여러 규칙은 `and` 논리 연산자를 사용하여 평가됩니다.

  **규칙** 은 다음 중 하나일 수 있습니다.

  |규칙 종류        |Syntax          |Predicate    |
  |-----------------|--------------|-------------------------|
  |이름 기준 같음 |*ColumnName*    |`where` *LeftTable*.*ColumnName* `==` *RightTable*.*ColumnName*|
  |값 기준 같음|`$left.`*LeftColumn* `==` `$right.`*RightColumn*|`where` `$left.`*LeftColumn* `==` `$right.`*RightColumn*       |

    > [!NOTE]
    > '값 기준 같음'의 경우 열 이름은 `$left` 및 `$right` 표기법으로 표시되는 해당 소유자 테이블로 *한정해야 합니다*.

* *JoinParameters*: 행 일치 작업 및 실행 계획의 동작을 제어하는 *이름* `=` *값* 형식의 0개 이상의 매개 변수(공백으로 구분)입니다. 지원되는 매개 변수는 다음과 같습니다.

    ::: zone pivot="azuredataexplorer"

    |매개 변수 이름           |값                                        |설명                                  |
    |---------------|----------------------------------------------|---------------------------------------------|
    |`kind`         |조인의 다양한 특징|[조인의 다양한 특징](#join-flavors) 참조|                                             |
    |`hint.remote`  |`auto`, `left`, `local`, `right`              |[클러스터 간 조인](joincrosscluster.md) 참조|
    |`hint.strategy`|실행 힌트                               |[조인 힌트](#join-hints) 참조                |

    ::: zone-end

    ::: zone pivot="azuremonitor"

    |Name           |값                                        |설명                                  |
    |---------------|----------------------------------------------|---------------------------------------------|
    |`kind`         |조인의 다양한 특징|[조인의 다양한 특징](#join-flavors) 참조|                                             |
    |`hint.remote`  |`auto`, `left`, `local`, `right`              |                                             |
    |`hint.strategy`|실행 힌트                               |[조인 힌트](#join-hints) 참조                |

    ::: zone-end

> [!WARNING]
> `kind`가 지정되지 않으면 기본 조인 특징은 `innerunique`입니다. 이는 기본 특징으로 `inner`가 있는 일부 다른 분석 제품과 다릅니다.  차이점을 이해하고 쿼리에서 원하는 결과를 생성하는지 확인하려면 [조인의 다양한 특징](#join-flavors)을 참조하세요.

## <a name="returns"></a>반환

**출력 스키마는 조인 특징에 따라 달라집니다.**

| 조인 특징 | 출력 스키마입니다. |
|---|---|
|`kind=leftanti`, `kind=leftsemi`| 결과 테이블에 왼쪽의 열만 포함됩니다.|
| `kind=rightanti`, `kind=rightsemi` | 결과 테이블에 오른쪽의 열만 포함됩니다.|
|  `kind=innerunique`, `kind=inner`, `kind=leftouter`, `kind=rightouter`, `kind=fullouter` |  일치하는 키를 포함하여 두 테이블 각각의 모든 열에 대한 열. 이름 충돌이 있는 경우 우변의 열 이름이 자동으로 바뀝니다. |
   
**출력 레코드는 조인 특징에 따라 달라집니다.**

   > [!NOTE]
   > 이러한 필드에 대해 같은 값을 가진 여러 행이 있는 경우 모든 조합에 대한 행을 얻습니다.
   > 일치 항목은 한 테이블에서 선택된 행이며 모든 `on` 필드에 대해 다른 테이블의 행과 같은 값을 가지고 있습니다.

| 조인 특징 | 출력 레코드 |
|---|---|
|`kind=leftanti`, `kind=leftantisemi`| 오른쪽에서 일치하는 항목이 없는 왼쪽의 모든 레코드를 반환합니다.|
| `kind=rightanti`, `kind=rightantisemi`| 왼쪽에서 일치하는 항목이 없는 오른쪽의 모든 레코드를 반환합니다.|
| `kind` 지정되지 않음, `kind=innerunique`| `on` 키의 각 값에 대해 좌변의 한 개의 행만 일치합니다. 출력은 오른쪽부터 이 행과 행 일치 각각에 대해 한 개의 행을 포함하고 있습니다.|
| `kind=leftsemi`| 오른쪽에서 일치하는 항목이 있는 왼쪽의 모든 레코드를 반환합니다. |
| `kind=rightsemi`| 왼쪽에서 일치하는 항목이 있는 오른쪽의 모든 레코드를 반환합니다. |
|`kind=inner`| 왼쪽 및 오른쪽에서 일치하는 행의 모든 조합에 대한 하나의 행이 출력에 포함됩니다. |
| `kind=leftouter`(또는 `kind=rightouter` 또는 `kind=fullouter`)| 일치하는 항목이 없는 경우에도 왼쪽 및 오른쪽의 모든 행에 대한 하나의 행이 포함됩니다. 일치하지 않는 출력 셀에는 null이 포함됩니다. |

> [!TIP]
> 최상의 성능을 위해 한 테이블이 항상 다른 테이블보다 작으면 해당 테이블을 조인의 왼쪽(파이프됨)으로 사용합니다.

## <a name="example"></a>예제

일부 항목이 작업의 시작 및 끝으로 표시되는 `login`에서 확장된 작업을 가져옵니다.

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

## <a name="join-flavors"></a>조인의 다양한 특징

join 연산자의 정확한 특징은 *kind* 키워드를 사용하여 지정됩니다. 지원되는 join 연산자의 특징은 다음과 같습니다.

|조인 종류/특징|설명|
|--|--|
|[`innerunique`](#default-join-flavor)(또는 기본값으로 비어 있음)|왼쪽 중복 제거를 사용하는 내부 조인|
|[`inner`](#inner-join-flavor)|표준 내부 조인|
|[`leftouter`](#left-outer-join-flavor)|왼쪽 우선 외부 조인|
|[`rightouter`](#right-outer-join-flavor)|오른쪽 우선 외부 조인|
|[`fullouter`](#full-outer-join-flavor)|완전 외부 조인|
|[`leftanti`](#left-anti-join-flavor), [`anti`](#left-anti-join-flavor) 또는 [`leftantisemi`](#left-anti-join-flavor)|왼쪽 우선 안티 조인|
|[`rightanti`](#right-anti-join-flavor) 또는 [`rightantisemi`](#right-anti-join-flavor)|오른쪽 우선 안티 조인|
|[`leftsemi`](#left-semi-join-flavor)|왼쪽 우선 세미 조인|
|[`rightsemi`](#right-semi-join-flavor)|오른쪽 우선 세미 조인|

### <a name="default-join-flavor"></a>기본 조인 특징

기본 조인 특징은 왼쪽 중복 제거를 사용하는 내부 조인입니다. 기본 조인 구현은 동일한 상관 관계 ID에서 각각 일부 필터링 조건과 일치하는 두 이벤트의 상관 관계를 지정하려는 일반적인 로그/추적 분석 시나리오에서 유용합니다. 현상의 모든 모양을 다시 가져오고, 영향을 받는 추적 레코드의 여러 모양을 무시하려고 합니다.

``` 
X | join Y on Key
 
X | join kind=innerunique Y on Key
```

다음 두 가지 샘플 테이블은 조인 작업을 설명하는 데 사용됩니다.

**테이블 X**

|키 |값1
|---|---
|a |1
|b |2
|b |3
|c |4

**테이블 Y**

|키 |값2
|---|---
|b |10
|c |20
|c |30
|일 |40

기본 조인은 조인 키의 왼쪽을 중복 제거한 후에 내부 조인을 수행합니다(중복 제거에서 첫 번째 레코드는 유지됨).

`X | join Y on Key` 문이 지정되는 경우

중복 제거 후 조인의 효과적인 왼쪽인 테이블 X는 다음과 같습니다.

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

|키|값1|키1|값2|
|---|---|---|---|
|b|2|b|10|
|c|4|c|20|
|c|4|c|30|

> [!NOTE]
> 왼쪽과 오른쪽 모두에 일치하는 키가 없으므로 'a' 및 'd' 키는 출력에 표시되지 않습니다.

### <a name="inner-join-flavor"></a>내부 조인 특징

내부 조인 함수는 SQL 세계의 표준 내부 조인과 같습니다. 왼쪽의 레코드와 오른쪽의 레코드의 조인 키가 동일할 때마다 출력 레코드가 생성됩니다.

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

|키|값1|키1|값2|
|---|---|---|---|
|b|3|b|10|
|b|2|b|10|
|c|4|c|20|
|c|4|c|30|

> [!NOTE]
> * 오른쪽의 (b,10)은 왼쪽의 (b,2) 및 (b,3) 모두와 두 번 조인되었습니다.
> * 왼쪽의 (c,4)는 오른쪽의 (c,20) 및 (c,30) 모두와 두 번 조인되었습니다.

### <a name="innerunique-join-flavor"></a>innerunique 조인 특징
 
**innerunique 조인 특징** 을 사용하여 왼쪽에서 키를 중복 제거합니다. 결과는 중복 제거된 왼쪽 키 및 오른쪽 키의 모든 조합에서 출력되는 행입니다.

> [!NOTE]
> **innerunique 특징** 은 가능한 두 개의 출력을 생성할 수 있으며 둘 다 정확합니다.
첫 번째 출력에서 join 연산자는 값이 "val1.1"인 t1에 표시되는 첫 번째 키를 임의로 선택하고 t2 키와 일치시켰습니다.
두 번째 출력에서 join 연산자는 값이 "val1.2"인 t1에 표시되는 두 번째 키를 임의로 선택하고 t2 키와 일치시켰습니다.

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

|key|값|key1|value1|
|---|---|---|---|
|1|val1.2|1|val1.3|
|1|val1.2|1|val1.4|

* Kusto는 가능한 경우 `join` 뒤에 나오는 필터를 적절한 조인 쪽(왼쪽 또는 오른)으로 푸시하도록 최적화되어 있습니다.

* 경우에 따라 사용되는 특징은 **innerunique** 이고 필터가 조인의 왼쪽으로 전파됩니다. 특징이 자동으로 전파되고, 해당 필터에 적용되는 키가 항상 출력에 표시됩니다.
    
* 위의 예제를 사용하여 `where value == "val1.2" ` 필터를 추가합니다. 항상 두 번째 결과를 제공하며, 데이터 세트에 대한 첫 번째 결과는 제공하지 않습니다.

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
|1|val1.2|1|val1.3|
|1|val1.2|1|val1.4|

### <a name="left-outer-join-flavor"></a>왼쪽 우선 외부 조인 특징

조인 조건이 오른쪽 테이블(Y)에서 일치하는 레코드를 찾지 못하는 경우에도 테이블 X 및 Y에 대한 왼쪽 우선 외부 조인의 결과에는 항상 왼쪽 테이블(X)의 모든 레코드가 포함됩니다.

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

|키|값1|키1|값2|
|---|---|---|---|
|b|3|b|10|
|b|2|b|10|
|c|4|c|20|
|c|4|c|30|
|a|1|||

### <a name="right-outer-join-flavor"></a>오른쪽 우선 외부 조인 특징

오른쪽 우선 외부 조인 특징은 왼쪽 우선 외부 조인 특징과 비슷하지만 테이블 처리가 반대입니다.

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

|키|값1|키1|값2|
|---|---|---|---|
|b|3|b|10|
|b|2|b|10|
|c|4|c|20|
|c|4|c|30|
|||일|40|

### <a name="full-outer-join-flavor"></a>완전 외부 조인 특징

완전 외부 조인은 왼쪽 및 오른쪽 우선 외부 조인을 모두 적용한 효과를 결합합니다. 조인된 테이블의 레코드가 일치하지 않을 때마다 일치하는 행이 없는 테이블의 모든 열에 대한 `null` 값이 결과 집합에 포함됩니다. 일치하는 레코드의 경우 두 테이블에서 채워진 필드가 포함된 단일 행이 결과 집합에 생성됩니다.

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

|키|값1|키1|값2|
|---|---|---|---|
|b|3|b|10|
|b|2|b|10|
|c|4|c|20|
|c|4|c|30|
|||일|40|
|a|1|||

### <a name="left-anti-join-flavor"></a>왼쪽 우선 안티 조인 특징

왼쪽 우선 안티 조인은 오른쪽의 레코드와 일치하지 않는 왼쪽의 모든 레코드를 반환합니다.

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

> [!NOTE]
> 앤티 조인은 “NOT IN” 쿼리를 모델링합니다.

### <a name="right-anti-join-flavor"></a>오른쪽 우선 안티 조인 특징

오른쪽 우선 안티 조인은 왼쪽의 레코드와 일치하지 않는 오른쪽의 모든 레코드를 반환합니다.

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

> [!NOTE]
> 앤티 조인은 “NOT IN” 쿼리를 모델링합니다.

### <a name="left-semi-join-flavor"></a>왼쪽 우선 세미 조인 특징

왼쪽 우선 세미 조인은 오른쪽의 레코드와 일치하는 왼쪽의 모든 레코드를 반환합니다. 왼쪽의 열만 반환됩니다.

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

### <a name="right-semi-join-flavor"></a>오른쪽 우선 세미 조인 특징

오른쪽 우선 세미 조인은 왼쪽의 레코드와 일치하는 오른쪽의 모든 레코드를 반환합니다. 오른쪽의 열만 반환됩니다.

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

Kusto는 기본적으로 크로스 조인 특징을 제공하지 않습니다. 연산자는 `kind=cross`를 사용하여 표시할 수 없습니다.
시뮬레이션하려면 더미 키를 사용합니다.

`X | extend dummy=1 | join kind=inner (Y | extend dummy=1) on dummy`

## <a name="join-hints"></a>조인 힌트

`join` 연산자는 쿼리가 실행되는 방법을 제어하는 여러 힌트를 지원합니다.
이러한 힌트는 `join`의 의미 체계를 변경하지 않지만 성능에는 영향을 줄 수 있습니다.

조인 힌트는 다음 문서에서 설명합니다.

* `hint.shufflekey=<key>` 및 `hint.strategy=shuffle` - [쿼리 순서 섞기](shufflequery.md)
* `hint.strategy=broadcast` - [브로드캐스트 조인](broadcastjoin.md)
* `hint.remote=<strategy>` - [클러스터 간 조인](joincrosscluster.md)
