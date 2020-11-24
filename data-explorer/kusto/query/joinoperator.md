---
title: 조인 연산자-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 조인 연산자에 대해 설명 합니다.
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
ms.sourcegitcommit: 4e811d2f50d41c6e220b4ab1009bb81be08e7d84
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2020
ms.locfileid: "95513338"
---
# <a name="join-operator"></a>join 연산자

각 테이블의 지정 된 열 값을 일치 시켜 새 테이블을 구성 하려면 두 테이블의 행을 병합 합니다.

```kusto
Table1 | join (Table2) on CommonColumn, $left.Col1 == $right.Col2
```

## <a name="syntax"></a>구문

*왼쪽 테이블* `|` `join` [*Joinparameters*] `(` *righttable* `)` `on` *특성*

## <a name="arguments"></a>인수

* 왼쪽 *테이블*: 행이 병합 되는 **외부** 테이블이 라고도 하는 **왼쪽** 테이블 또는 테이블 형식 식입니다. 로 표시 `$left` 됩니다.

* *Righttable*: 행을 병합 하는 데 사용할 수 있는 **오른쪽** 테이블 또는 테이블 형식 식 ( **내부** 테이블)입니다. 로 표시 `$right` 됩니다.

* *특성*: *왼쪽 테이블* 의 행이 *righttable* 의 행과 일치 하는 방식을 설명 하는 쉼표로 구분 된 하나 이상의 **규칙** 입니다. 논리 연산자를 사용 하 여 여러 규칙을 평가 `and` 합니다.

  **규칙** 은 다음 중 하나일 수 있습니다.

  |규칙 종류        |구문          |Predicate    |
  |-----------------|--------------|-------------------------|
  |이름으로 같음 |*ColumnName*    |`where`*왼쪽 테이블*. *ColumnName* `==` *Righttable*. *ColumnName*|
  |값으로 같음|`$left.`*왼쪽 열* `==` `$right.` *Rightcolumn*|`where``$left.` *왼쪽* `==` 열 `$right.` *Rightcolumn*       |

    > [!NOTE]
    > ' 값으로 같음 '의 경우 열 이름은 및 표기법으로 표시 된 해당 소유자 테이블로 *한정 되어야* 합니다 `$left` `$right` .

* *Joinparameters*: *Name* `=` 행 일치 작업 및 실행 계획의 동작을 제어 하는 이름 *값* 형식의 공백으로 구분 된 0 개 이상의 매개 변수입니다. 지원 되는 매개 변수는 다음과 같습니다.

    ::: zone pivot="azuredataexplorer"

    |매개 변수 이름           |값                                        |설명                                  |
    |---------------|----------------------------------------------|---------------------------------------------|
    |`kind`         |조인의 다양한 특징|[조인 특색](#join-flavors) 참조|                                             |
    |`hint.remote`  |`auto`, `left`, `local`, `right`              |[클러스터 간 조인](joincrosscluster.md) 참조|
    |`hint.strategy`|실행 힌트                               |[조인 힌트](#join-hints) 참조                |

    ::: zone-end

    ::: zone pivot="azuremonitor"

    |속성           |값                                        |설명                                  |
    |---------------|----------------------------------------------|---------------------------------------------|
    |`kind`         |조인의 다양한 특징|[조인 특색](#join-flavors) 참조|                                             |
    |`hint.remote`  |`auto`, `left`, `local`, `right`              |                                             |
    |`hint.strategy`|실행 힌트                               |[조인 힌트](#join-hints) 참조                |

    ::: zone-end

> [!WARNING]
> `kind`을 지정 하지 않으면 기본 조인 버전은 `innerunique` 입니다. 이는 기본 버전으로가 있는 일부 다른 분석 제품과는 다릅니다 `inner` .  [조인 특색](#join-flavors) 을 참조 하 여 차이점을 이해 하 고 쿼리가 의도 한 결과를 생성 하는지 확인 합니다.

## <a name="returns"></a>반환

**출력 스키마는 조인 버전에 따라 달라 집니다.**

| 조인 버전 | 출력 스키마입니다. |
|---|---|
|`kind=leftanti`, `kind=leftsemi`| 결과 테이블은 좌 변에 있는 열만 포함 합니다.|
| `kind=rightanti`, `kind=rightsemi` | 결과 테이블에는 우변에 있는 열만 포함 됩니다.|
|  `kind=innerunique`, `kind=inner`, `kind=leftouter`, `kind=rightouter`, `kind=fullouter` |  일치하는 키를 포함하여 두 테이블 각각의 모든 열에 대한 열. 이름 충돌이 있는 경우 우변의 열 이름이 자동으로 바뀝니다. |
   
**출력 레코드는 조인 버전에 따라 달라 집니다.**

   > [!NOTE]
   > 이러한 필드에 대해 같은 값을 가진 여러 행이 있는 경우 모든 조합에 대한 행을 얻습니다.
   > 일치 항목은 한 테이블에서 선택된 행이며 모든 `on` 필드에 대해 다른 테이블의 행과 같은 값을 가지고 있습니다.

| 조인 버전 | 출력 레코드 |
|---|---|
|`kind=leftanti`, `kind=leftantisemi`| 오른쪽에서 일치 하는 항목이 없는 왼쪽의 모든 레코드를 반환 합니다.|
| `kind=rightanti`, `kind=rightantisemi`| 왼쪽에서 일치 하는 항목이 없는 우변의 모든 레코드를 반환 합니다.|
| `kind` 지정 되지 않은 `kind=innerunique`| `on` 키의 각 값에 대해 좌변의 한 개의 행만 일치합니다. 출력은 오른쪽부터 이 행과 행 일치 각각에 대해 한 개의 행을 포함하고 있습니다.|
| `kind=leftsemi`| 오른쪽에서 일치 하는 항목이 있는 왼쪽의 모든 레코드를 반환 합니다. |
| `kind=rightsemi`| 왼쪽에서 일치 하는 항목을 포함 하는 오른쪽의 모든 레코드를 반환 합니다. |
|`kind=inner`| 왼쪽 및 오른쪽에서 일치 하는 행의 모든 조합에 대해 출력에 행을 포함 합니다. |
| `kind=leftouter`(또는 `kind=rightouter` 또는 `kind=fullouter`)| 일치 하는 항목이 없는 경우에도 왼쪽 및 오른쪽의 모든 행에 대 한 행을 포함 합니다. 일치 하지 않는 출력 셀에는 null이 포함 됩니다. |

> [!TIP]
> 최상의 성능을 위해 한 테이블이 다른 테이블 보다 항상 작은 경우 조인에서 파이프 된 왼쪽으로 사용 합니다.

## <a name="example"></a>예제

`login`일부 항목이 활동의 시작 및 끝으로 표시 되는에서 확장 된 활동을 가져옵니다.

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

Join 연산자의 정확한 버전은 *kind* 키워드를 사용 하 여 지정 됩니다. 조인 연산자의 다음과 같은 종류가 지원 됩니다.

|조인 종류/버전|설명|
|--|--|
|[`innerunique`](#default-join-flavor) (또는 기본값으로 비어 있음)|왼쪽 중복 제거를 사용 하는 내부 조인|
|[`inner`](#inner-join-flavor)|표준 내부 조인|
|[`leftouter`](#left-outer-join-flavor)|왼쪽 우선 외부 조인|
|[`rightouter`](#right-outer-join-flavor)|오른쪽 우선 외부 조인|
|[`fullouter`](#full-outer-join-flavor)|완전 외부 조인|
|[`leftanti`](#left-anti-join-flavor), [`anti`](#left-anti-join-flavor) 또는 [`leftantisemi`](#left-anti-join-flavor)|왼쪽 우선 안티 조인|
|[`rightanti`](#right-anti-join-flavor) 또는 [`rightantisemi`](#right-anti-join-flavor)|오른쪽 우선 안티 조인|
|[`leftsemi`](#left-semi-join-flavor)|왼쪽 세미 조인|
|[`rightsemi`](#right-semi-join-flavor)|오른쪽 세미 조인|

### <a name="default-join-flavor"></a>기본 조인 버전

기본 조인 버전은 왼쪽 중복 제거를 사용 하는 내부 조인입니다. 기본 조인 구현은 동일한 상관 관계 ID에서 각각의 필터링 조건과 일치 하는 두 이벤트의 상관 관계를 지정할 수 있는 일반적인 로그/추적 분석 시나리오에서 유용 합니다. 현상의 모든 모양을 다시 가져오고 영향을 받는 추적 레코드의 여러 모양을 무시 하려고 합니다.

``` 
X | join Y on Key
 
X | join kind=innerunique Y on Key
```

다음 두 가지 예제 테이블은 조인 작업을 설명 하는 데 사용 됩니다.

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

기본 조인은 조인 키에서 왼쪽을 복제 한 후 내부 조인을 수행 합니다 (중복 제거는 첫 번째 레코드를 유지 합니다).

다음 문이 제공 됩니다. `X | join Y on Key`

조인 왼쪽에 있는 표 X는 중복 제거 후에 다음을 수행 합니다.

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

> [!NOTE]
> 왼쪽 및 오른쪽 모두에 일치 하는 키가 없으므로 ' a ' 및 ' d ' 키는 출력에 표시 되지 않습니다.

### <a name="inner-join-flavor"></a>내부 조인 버전

내부 조인 함수는 SQL 세계의 표준 내부 조인과 비슷합니다. 출력 레코드는 좌 변의 레코드와 우변에 있는 레코드의 조인 키가 동일할 때마다 생성 됩니다.

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

> [!NOTE]
> * 오른쪽에서 (b, 10)은 두 번 조인 됩니다. (b, 2) 및 (b, 3)은 왼쪽에 있습니다.
> * 왼쪽에 (c, 4)가 두 번 조인 되었습니다. (c, 20) 및 (c, 30)이 오른쪽에 있습니다.

### <a name="innerunique-join-flavor"></a>Innerunique 조인 버전
 
**Innerunique 조인 버전** 을 사용 하 여 왼쪽에서 중복 키를 제거 합니다. 그러면 중복 제거 된 왼쪽 키와 오른쪽 키의 모든 조합에서 출력의 행이 반환 됩니다.

> [!NOTE]
> **innerunique 버전** 은 가능한 두 개의 출력을 생성할 수 있으며 둘 다 올바릅니다.
첫 번째 출력에서 조인 연산자는 t1에 표시 되는 첫 번째 키 (값 "val 1.1")를 임의로 선택 하 고 t2 키와 일치 시킵니다.
두 번째 출력에서 조인 연산자는 t1에 표시 되는 두 번째 키 (값 "val 1.2")를 임의로 선택 하 고 t2 키와 일치 시킵니다.

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

* Kusto는 `join` 가능한 경우 적절 한 조인 측, 왼쪽 또는 오른쪽으로 뒤에 오는 필터를 푸시하는 데 최적화 되어 있습니다.

* 경우에 따라 사용 되는 버전은 **innerunique** 이 고 필터는 조인의 왼쪽으로 전파 됩니다. 버전은 자동으로 전파 되 고 해당 필터에 적용 되는 키는 항상 출력에 표시 됩니다.
    
* 위의 예제를 사용 하 여 필터를 추가 `where value == "val1.2" ` 합니다. 항상 두 번째 결과를 제공 하 고 데이터 집합에 대 한 첫 번째 결과를 제공 하지 않습니다.

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

### <a name="left-outer-join-flavor"></a>왼쪽 우선 외부 조인 버전

X 및 Y 테이블에 대 한 왼쪽 우선 외부 조인의 결과는 조인 조건이 오른쪽 테이블 (Y)에서 일치 하는 레코드를 찾지 못하는 경우에도 항상 왼쪽 테이블 (X)의 모든 레코드를 포함 합니다.

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

### <a name="right-outer-join-flavor"></a>오른쪽 우선 외부 조인 버전

오른쪽 외부 조인 버전은 왼쪽 우선 외부 조인과 비슷하지만 테이블 처리는 반대입니다.

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

### <a name="full-outer-join-flavor"></a>완전 외부 조인 버전

완전 외부 조인은 왼쪽과 오른쪽 외부 조인을 적용 한 효과를 결합 합니다. 조인 된 테이블의 레코드가 일치 하지 않을 때마다 결과 집합에는 일치 하는 행이 없는 `null` 테이블의 모든 열에 대 한 값이 포함 됩니다. 일치 하는 레코드에 대해 두 테이블에서 채워진 필드가 포함 된 단일 행이 결과 집합에 생성 됩니다.

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

### <a name="left-anti-join-flavor"></a>왼쪽 앤티 조인 버전

왼쪽 앤티 조인은 우변에 있는 레코드와 일치 하지 않는 왼쪽의 모든 레코드를 반환 합니다.

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

### <a name="right-anti-join-flavor"></a>오른쪽 앤티 조인 버전

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

> [!NOTE]
> 앤티 조인은 “NOT IN” 쿼리를 모델링합니다.

### <a name="left-semi-join-flavor"></a>왼쪽 세미 조인 버전

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

### <a name="right-semi-join-flavor"></a>오른쪽 세미 조인 버전

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

Kusto는 기본적으로 크로스 조인 버전을 제공 하지 않습니다. 연산자는로 표시할 수 없습니다 `kind=cross` .
시뮬레이션 하려면 더미 키를 사용 합니다.

`X | extend dummy=1 | join kind=inner (Y | extend dummy=1) on dummy`

## <a name="join-hints"></a>조인 힌트

`join`연산자는 쿼리가 실행 되는 방식을 제어 하는 여러 힌트를 지원 합니다.
이러한 힌트는의 의미 체계를 변경 하지 `join` 않지만 성능에 영향을 줄 수 있습니다.

조인 힌트는 다음 문서에서 설명 합니다.

* `hint.shufflekey=<key>``hint.strategy=shuffle`  -  [쿼리 순서 섞기](shufflequery.md)
* `hint.strategy=broadcast` - [브로드캐스트 조인](broadcastjoin.md)
* `hint.remote=<strategy>` - [클러스터 간 조인](joincrosscluster.md)
