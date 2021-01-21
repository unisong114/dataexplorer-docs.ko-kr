---
title: union 연산자 - Azure Data Explorer | Microsoft Docs
description: 이 문서에서는 Azure Data Explorer의 union 연산자에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.localizationpriority: high
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 3ce7c2c09e9cd5449accfabc2a7e1cc21e4ed339
ms.sourcegitcommit: d4b359e817e002fba7320132732ce6d9cee97415
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/18/2021
ms.locfileid: "98541481"
---
# <a name="union-operator"></a>union 연산자

두 개 이상의 테이블을 사용하며 모든 행을 반환합니다. 

```kusto
Table1 | union Table2, Table3
```

## <a name="syntax"></a>Syntax

*T* `| union` [*UnionParameters*] [`kind=` `inner`|`outer`] [`withsource=`*ColumnName*] [`isfuzzy=` `true`|`false`] *Table* [`,` *Table*]...  

파이프된 입력이 없는 대체 양식:

`union` [*UnionParameters*] [`kind=` `inner`|`outer`] [`withsource=`*ColumnName*] [`isfuzzy=` `true`|`false`] *Table* [`,` *Table*]...  

## <a name="arguments"></a>인수

::: zone pivot="azuredataexplorer"

* `Table`:
    *  `Events`등과 같은 테이블의 이름, 또는
    *  괄호로 묶어야 하는 쿼리 식입니다(예: `(Events | where id==42)` 또는 `(cluster("https://help.kusto.windows.net:443").database("Samples").table("*"))`). 또는
    *  와일드 카드를 사용하여 지정한 테이블 집합입니다. 예를 들어 `E*`는 데이터베이스에서 `E`로 시작하는 이름의 모든 테이블에 대한 합집합을 만듭니다.
* `kind`: 
    * `inner` - 결과에는 모든 입력 테이블에 공통인 열의 하위 집합이 있습니다.
    * `outer` - (기본값). 입력에서 발생하는 모든 열이 결과에 포함됩니다. 입력 행에서 정의되지 않은 셀은 `null`로 설정됩니다.
* `withsource`=*ColumnName*: 지정되면 *ColumnName* 이라는 열이 출력에 포함됩니다. 해당 값은 각 행에 기여한 원본 테이블을 나타냅니다.
쿼리에서 둘 이상의 데이터베이스(기본 데이터베이스는 항상 계산됨)에 있는 테이블을 효과적으로(와일드카드 일치 후) 참조하는 경우 이 열의 값에는 데이터베이스로 한정된 테이블 이름이 포함됩니다.
이와 비슷하게 둘 이상의 클러스터를 참조하는 경우 __클러스터 및 데이터베이스__ 한정자가 값에 표시됩니다. 
* `isfuzzy=` `true` | `false`: `isfuzzy`가 `true`로 설정되면 union 레그의 유사 항목 확인을 허용합니다. `Fuzzy`는 `union` 원본 집합에 적용됩니다. 즉, 쿼리를 분석하고 실행을 준비하는 동안 union 원본 집합이 해당 시점에서 존재하고 액세스할 수 있는 테이블 참조 집합으로 축소됩니다. 이러한 테이블이 하나 이상 발견되면 확인 실패에 따라 경고를 쿼리 상태 결과에 생성하지만(누락 참조당 하나씩) 쿼리 실행을 방해하지는 않습니다. 성공적으로 확인하지 못하면 쿼리에서 오류가 반환됩니다.
기본값은 `isfuzzy=` `false`입니다.
* *UnionParameters*: 행 일치 작업 및 실행 계획의 동작을 제어하는 *이름* `=` *값* 형식의 0개 이상의 매개 변수(공백으로 구분)입니다. 지원되는 매개 변수는 다음과 같습니다. 

  |Name           |값                                        |설명                                  |
  |---------------|----------------------------------------------|---------------------------------------------|
  |`hint.concurrency`|*Number*|동시에 실행해야 하는 `union` 연산자의 동시 하위 쿼리 수에 대한 힌트를 시스템에 제공합니다. *기본값*: 클러스터의 단일 노드에 있는 CPU 코어의 양(2~16)입니다.|
  |`hint.spread`|*Number*|동시 `union` 하위 쿼리를 실행하는 데 사용해야 하는 노드 수에 대한 힌트를 시스템에 제공합니다. *기본값*: 1.|

::: zone-end

::: zone pivot="azuremonitor"

* `Table`:
    *  테이블 이름입니다(예: `Events`).
    *  괄호로 묶어야 하는 쿼리 식입니다(예: `(Events | where id==42)`).
    *  와일드 카드를 사용하여 지정한 테이블 집합입니다. 예를 들어 `E*`는 데이터베이스에서 `E`로 시작하는 이름의 모든 테이블에 대한 합집합을 만듭니다.

> [!NOTE]
> 테이블 목록을 아는 경우 와일드카드를 사용하지 마세요. 테이블이 매우 많아서 비효율적으로 실행될 수 있는 작업 영역도 있습니다. 또한 테이블이 점점 추가되어 예기치 못한 결과를 초래할 수도 있습니다.
    
* `kind`: 
    * `inner` - 결과에는 모든 입력 테이블에 공통인 열의 하위 집합이 있습니다.
    * `outer` - (기본값). 입력에서 발생하는 모든 열이 결과에 포함됩니다. 입력 행에서 정의되지 않은 셀은 `null`로 설정됩니다.
* `withsource`=*ColumnName*: 지정되면 *ColumnName* 이라는 열이 출력에 포함됩니다. 해당 값은 각 행에 기여한 원본 테이블을 나타냅니다.
쿼리에서 둘 이상의 데이터베이스(기본 데이터베이스는 항상 계산됨)에 있는 테이블을 효과적으로(와일드카드 일치 후) 참조하는 경우 이 열의 값에는 데이터베이스로 한정된 테이블 이름이 포함됩니다.
이와 비슷하게 둘 이상의 클러스터를 참조하는 경우 __클러스터 및 데이터베이스__ 한정자가 값에 표시됩니다. 
* `isfuzzy=` `true` | `false`: `isfuzzy`가 `true`로 설정되면 union 레그의 유사 항목 확인을 허용합니다. `Fuzzy`는 `union` 원본 집합에 적용됩니다. 즉, 쿼리를 분석하고 실행을 준비하는 동안 union 원본 집합이 해당 시점에서 존재하고 액세스할 수 있는 테이블 참조 집합으로 축소됩니다. 이러한 테이블이 하나 이상 발견되면 확인 실패에 따라 경고를 쿼리 상태 결과에 생성하지만(누락 참조당 하나씩) 쿼리 실행을 방해하지는 않습니다. 성공적으로 확인하지 못하면 쿼리에서 오류가 반환됩니다.
기본값은 `isfuzzy=false`입니다.

::: zone-end

## <a name="returns"></a>반환

모든 입력된 테이블에 있는 만큼 행을 가진 테이블입니다.

**참고**

::: zone pivot="azuredataexplorer"

1. [let 문](./letstatement.md)에서 [view 키워드](./letstatement.md) 특성을 사용하면 `union` 범위에는 해당 명령문이 포함될 수 있습니다.
2. `union` 범위에는 [함수](../management/functions.md)가 포함되지 않습니다. 함수를 union 범위에 포함하려면 [view 키워드](./letstatement.md)를 사용하여 [let 문](./letstatement.md)을 정의합니다.
3. `union` 입력이 [테이블](../management/tables.md)이고([테이블 형식 식](./tabularexpressionstatements.md)과 반대) `union` 뒤에 [where 연산자](./whereoperator.md)가 나오는 경우 성능을 향상시키기 위해 둘 다 [find](./findoperator.md)로 바꾸는 것이 좋습니다. `find` 연산자에서 생성하는 다른 [출력 스키마](./findoperator.md#output-schema)를 확인하세요. 
4. `isfuzzy=true`는 `union` 원본 확인 단계에만 적용됩니다. 원본 테이블 집합이 결정되면 가능한 추가 쿼리 실패가 표시되지 않습니다.
5. `outer union`을 사용하는 경우 입력에서 발생하는 모든 열이 결과에 포함되며, 각 이름 및 형식 발생에 대해 하나의 열이 있습니다. 즉, 열이 여러 테이블에 표시되고 여러 형식을 사용하면 `union` 결과의 각 형식에 해당하는 열이 포함됩니다. 이 열 이름에는 '_' 및 그 뒤에 원본 열 [형식](./scalar-data-types/index.md)으로 구성된 접미사가 붙습니다.

::: zone-end

::: zone pivot="azuremonitor"

1. [let 문](./letstatement.md)에서 [view 키워드](./letstatement.md) 특성을 사용하면 `union` 범위에는 해당 명령문이 포함될 수 있습니다.
2. `union` 범위에는 함수가 포함되지 않습니다. 함수를 union 범위에 포함하려면 [view 키워드](./letstatement.md)를 사용하여 [let 문](./letstatement.md)을 정의합니다.
3. `union` 입력이 테이블([테이블 형식](./tabularexpressionstatements.md) 식에 대한 oppose)이고 `union`이 다음에 [where 연산자](./whereoperator.md)를 사용 하는 경우 성능 향상을 위해[를 모두 바꿔 보세요.](./findoperator.md) `find` 연산자에서 생성하는 다른 [출력 스키마](./findoperator.md#output-schema)를 확인하세요. 
4. `isfuzzy=` `true`는 `union` 원본 확인 단계에만 적용됩니다. 원본 테이블 집합이 결정되면 가능한 추가 쿼리 실패가 표시되지 않습니다.
5. `outer union`을 사용하는 경우 입력에서 발생하는 모든 열이 결과에 포함되며, 각 이름 및 형식 발생에 대해 하나의 열이 있습니다. 즉, 열이 여러 테이블에 표시되고 여러 형식을 사용하면 `union` 결과의 각 형식에 해당하는 열이 포함됩니다. 이 열 이름에는 '_' 및 그 뒤에 원본 열 [형식](./scalar-data-types/index.md)으로 구성된 접미사가 붙습니다.

::: zone-end


## <a name="example-tables-with-string-in-name-or-column"></a>예제: 이름 또는 열에 문자열이 있는 테이블

```kusto
union K* | where * has "Kusto"
```

데이터베이스에서 이름이 `K`로 시작하고 `Kusto` 단어가 열에 포함된 모든 테이블의 행입니다.

## <a name="example-distinct-count"></a>예제: 고유 개수

```kusto
union withsource=SourceTable kind=outer Query, Command
| where Timestamp > ago(1d)
| summarize dcount(UserId)
```

전날에 `Query` 이벤트 또는 `Command` 이벤트를 생성한 고유 사용자 수입니다. 결과에서 'SourceTable' 열은 "쿼리" 또는 "명령"을 나타냅니다.

```kusto
Query
| where Timestamp > ago(1d)
| union withsource=SourceTable kind=outer 
   (Command | where Timestamp > ago(1d))
| summarize dcount(UserId)
```

더 효율적인 이 버전은 동일한 결과를 생성합니다. 합집합을 생성하기 전에 각 테이블을 필터링합니다.

**예: `isfuzzy=true`** 사용
 
```kusto     
// Using union isfuzzy=true to access non-existing view:                                     
let View_1 = view () { print x=1 };
let View_2 = view () { print x=1 };
let OtherView_1 = view () { print x=1 };
union isfuzzy=true
(View_1 | where x > 0), 
(View_2 | where x > 0),
(View_3 | where x > 0)
| count 
```

|개수|
|---|
|2|

쿼리 상태 관찰- `Failed to resolve entity 'View_3'`라는 경고가 반환됩니다.

```kusto
// Using union isfuzzy=true and wildcard access:
let View_1 = view () { print x=1 };
let View_2 = view () { print x=1 };
let OtherView_1 = view () { print x=1 };
union isfuzzy=true View*, SomeView*, OtherView*
| count 
```

|개수|
|---|
|3|

쿼리 상태 관찰- `Failed to resolve entity 'SomeView*'`라는 경고가 반환됩니다.

**예제: 원본 열 형식 불일치**
 
```kusto     
let View_1 = view () { print x=1 };
let View_2 = view () { print x=toint(2) };
union withsource=TableName View_1, View_2
```

|TableName|x_long|x_int|
|---------|------|-----|
|View_1   |1     |     |
|View_2   |      |2    |

```kusto     
let View_1 = view () { print x=1 };
let View_2 = view () { print x=toint(2) };
let View_3 = view () { print x_long=3 };
union withsource=TableName View_1, View_2, View_3 
```

|TableName|x_long1|x_int |x_long|
|---------|-------|------|------|
|View_1   |1      |      |      |
|View_2   |       |2     |      |
|View_3   |       |      |3     |

`View_1`의 `x` 열에서 `_long` 접미사를 받고, `x_long`이라는 열이 이미 결과 스키마에 있으므로 열 이름이 중복 제거되어 새 `x_long1` 열이 생성되었습니다.
