---
title: 공용 구조자 연산자 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 공용 구조자 연산자에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: b62c259e1abf1ff0e0e98a90ac4da5a000db5320
ms.sourcegitcommit: 01eb9aaf1df2ebd5002eb7ea7367a9ef85dc4f5d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81765411"
---
# <a name="union-operator"></a>union 연산자

두 개 이상의 테이블을 사용하며 모든 행을 반환합니다. 

```kusto
Table1 | union Table2, Table3
```

**구문**

*T* `| union` [*UnionParameters*] [`kind=` `inner` | `outer`[`isfuzzy=` `true` | `false`[`withsource=`*열 이름*] [ [ *표* `,` *]* 표 ] ...  

파이프 입력이 없는 대체 양식:

`union`[*유니온 매개 변수*] `kind=` `inner` |[] `outer``withsource=`[ [`isfuzzy=` `true` | `false`*[열 이름]*[ [ *[ 표* `,` *]*...  

**인수**

::: zone pivot="azuredataexplorer"

* `Table`:
    *  `Events`등과 같은 테이블의 이름, 또는
    *  와 `(Events | where id==42)` `(cluster("https://help.kusto.windows.net:443").database("Samples").table("*"))`같은 괄호로 동봉되어야 하는 쿼리 식입니다. 또는
    *  와일드 카드를 사용하여 지정한 테이블 집합입니다. 예를 들어 `E*` 이름이 시작되는 `E`데이터베이스의 모든 테이블의 공용 구조체를 형성합니다.
* `kind`: 
    * `inner` - 결과에는 모든 입력 테이블에 공통인 열의 하위 집합이 있습니다.
    * `outer`- (기본값). 결과에는 입력에서 발생하는 모든 열이 있습니다. 입력 행에 의해 정의되지 않은 셀은 `null`로 설정됩니다.
* `withsource`=*ColumnName*: 지정하면 출력에는 각 행에 기여한 소스 테이블을 나타내는 값이 있는 *ColumnName이라는* 열이 포함됩니다.
쿼리가 두 개 이상의 데이터베이스의 테이블을 효과적으로 참조하는 경우(기본 데이터베이스는 항상 계산) 이 열의 값에는 데이터베이스로 한정된 테이블 이름이 지정됩니다.
마찬가지로 두 개 이상의 __클러스터를__ 참조하는 경우 클러스터 및 데이터베이스 자격도 값에 표시됩니다. 
* `isfuzzy=``true` `isfuzzy` : 설정된 경우 `true` - 유니온 다리의 퍼지 해상도를 허용합니다.  |  `false` `Fuzzy``union` 소스 집합에 적용됩니다. 즉, 쿼리를 분석하고 실행을 준비하는 동안 공용 구조조 원본 집합이 현재 존재하고 액세스할 수 있는 테이블 참조 집합으로 줄어듭니다. 이러한 테이블이 하나 이상 발견되면 모든 확인 실패는 쿼리 상태 결과(누락된 각 참조에 대해 하나씩)에 경고를 생성하지만 쿼리 실행을 방지하지는 않습니다. 해결 방법이 성공하지 못하면 쿼리에 오류가 반환됩니다.
기본값은 `isfuzzy=` `false`입니다.
* *UnionParameters*: 행 일치 작업 및 실행 계획의 동작을 제어하는 *이름* `=` *값* 의 형태로 0 개 이상의 (공간 구분) 매개 변수입니다. 다음 매개 변수가 지원됩니다. 

  |이름           |값                                        |Description                                  |
  |---------------|----------------------------------------------|---------------------------------------------|
  |`hint.concurrency`|*수*|연산자의 동시 하위 쿼리 수를 병렬로 `union` 실행해야 하는지 시스템을 암시합니다. *기본값*: 클러스터의 단일 노드에 있는 CPU 코어의 양(2~16)입니다.|
  |`hint.spread`|*수*|동시 `union` 하위 쿼리 실행에서 사용해야 하는 노드 수를 시스템에 암시합니다. *기본값*: 1.|

::: zone-end

::: zone pivot="azuremonitor"

* `Table`:
    *  테이블의 이름(예:`Events`
    *  괄호로 동봉되어야 하는 쿼리 식(예:`(Events | where id==42)`
    *  와일드 카드를 사용하여 지정한 테이블 집합입니다. 예를 들어 `E*` 이름이 시작되는 `E`데이터베이스의 모든 테이블의 공용 구조체를 형성합니다.
* `kind`: 
    * `inner` - 결과에는 모든 입력 테이블에 공통인 열의 하위 집합이 있습니다.
    * `outer`- (기본값). 결과에는 입력에서 발생하는 모든 열이 있습니다. 입력 행에 의해 정의되지 않은 셀은 `null`로 설정됩니다.
* `withsource`=*ColumnName*: 지정하면 출력에는 *ColumnName이라는* 열이 포함되며 그 값은 각 행에 기여한 소스 테이블을 나타냅니다.
쿼리가 두 개 이상의 데이터베이스의 테이블을 효과적으로 참조하는 경우(기본 데이터베이스는 항상 계산) 이 열의 값에는 데이터베이스로 한정된 테이블 이름이 지정됩니다.
마찬가지로 두 개 이상의 __클러스터를__ 참조하는 경우 클러스터 및 데이터베이스 자격은 값에 표시됩니다. 
* `isfuzzy=``true` `isfuzzy` : 설정된 경우 `true` - 유니온 다리의 퍼지 해상도를 허용합니다.  |  `false` `Fuzzy``union` 소스 집합에 적용됩니다. 즉, 쿼리를 분석하고 실행을 준비하는 동안 공용 구조조 원본 집합이 현재 존재하고 액세스할 수 있는 테이블 참조 집합으로 줄어듭니다. 이러한 테이블이 하나 이상 발견되면 모든 확인 실패는 쿼리 상태 결과(누락된 각 참조에 대해 하나씩)에 경고를 생성하지만 쿼리 실행을 방지하지는 않습니다. 해결 방법이 성공하지 못하면 쿼리에 오류가 반환됩니다.
기본값은 `isfuzzy=false`입니다.

::: zone-end

**반환**

모든 입력된 테이블에 있는 만큼 행을 가진 테이블입니다.

**참고 사항**

::: zone pivot="azuredataexplorer"

1. `union`scope에는 view 키워드로 인해 어트리뷰트된 경우 [let 문이](./letstatement.md) 포함될 수 [있습니다.](./letstatement.md)
2. `union`범위는 [함수를](../management/functions.md)포함하지 않습니다. 공용 구조자 범위에 함수를 포함하려면 [view 키워드를](./letstatement.md) 사용하여 [let 문을](./letstatement.md) 정의합니다.
3. 입력이 `union` [테이블(테이블](../management/tables.md) 형식 [식과](./tabularexpressionstatements.md) `union` 반대로)이고 [다음에 다음의 연산자가 더](./whereoperator.md)나은 성능을 위해 두 를 [find로](./findoperator.md)대체하는 것이 좋습니다. 연산자가 생성한 다른 출력 `find` [스키마를](./findoperator.md#output-schema) 기록합니다. 
4. `isfuzzy=true``union` 소스 해결 단계에만 적용됩니다. 원본 테이블 집합이 결정되면 추가 쿼리 오류가 억제되지 않습니다.
5. 을 `outer union`사용하는 경우 결과에는 입력에서 발생하는 모든 열, 각 이름 및 형식 발생에 대해 하나의 열이 있습니다. 즉, 열이 여러 테이블에 나타나고 여러 형식이 있는 경우 결과의 각 `union`형식에 해당하는 열이 있습니다. 이 열 이름은 '_'와 원한 열 [유형이](./scalar-data-types/index.md)붙어 있습니다.

::: zone-end

::: zone pivot="azuremonitor"

1. `union`scope에는 view 키워드로 인해 어트리뷰트된 경우 [let 문이](./letstatement.md) 포함될 수 [있습니다.](./letstatement.md)
2. `union`범위는 함수를 포함하지 않습니다. 공용 구조자 범위에 함수를 포함하려면 [view 키워드를](./letstatement.md) 사용하여 [let 문을](./letstatement.md) 정의합니다.
3. `union` 입력이 테이블([테이블 형식](./tabularexpressionstatements.md) 식에 대한 oppose)이고 `union`이 다음에 [where 연산자](./whereoperator.md)를 사용 하는 경우 성능 향상을 위해[를 모두 바꿔 보세요.](./findoperator.md) 연산자가 생성한 다른 출력 `find` [스키마를](./findoperator.md#output-schema) 유의하십시오. 
4. `isfuzzy=``true` 소스 해상도의 단계에만 `union` 적용됩니다. 원본 테이블 집합이 결정되면 가능한 추가 쿼리 오류가 억제되지 않습니다.
5. 을 `outer union`사용하는 경우 결과에는 입력에서 발생하는 모든 열, 각 이름 및 형식 발생에 대해 하나의 열이 있습니다. 즉, 열이 여러 테이블에 나타나고 여러 형식이 있는 경우 결과의 각 `union`형식에 해당하는 열이 있습니다. 이 열 이름은 '_'와 원한 열 [유형이](./scalar-data-types/index.md)붙어 있습니다.

::: zone-end


**예제**

```kusto
union K* | where * has "Kusto"
```

name이 `K`로 시작하고 모든 열에 단어가 `Kusto`포함된 데이터베이스의 모든 테이블의 행.

**예제**

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

**예: 사용`isfuzzy=true`**
 
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

쿼리 상태 관찰 - 다음 경고가 반환되었습니다.`Failed to resolve entity 'View_3'`

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

쿼리 상태 관찰 - 다음 경고가 반환되었습니다.`Failed to resolve entity 'SomeView*'`

**예: 원본 열 형식 불일치**
 
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

접미사를 `x` `View_1` `_long`받은 열과 결과 스키마에 이미 명명된 `x_long` 열이 있으므로 열 이름이 중복 제거되어 새 열이 생성됩니다.`x_long1`
