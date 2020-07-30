---
title: union 연산자-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 union 연산자에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 6dd305ccf5e260666072a4ab2b0d8b6707894902
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87339496"
---
# <a name="union-operator"></a>union 연산자

두 개 이상의 테이블을 사용하며 모든 행을 반환합니다. 

```kusto
Table1 | union Table2, Table3
```

## <a name="syntax"></a>Syntax

*T* `| union` [*UnionParameters*] [ `kind=` `inner` | `outer` ] [ `withsource=` *ColumnName*] [ `isfuzzy=` `true` | `false` ] *Table* [ `,` *table*] ...  

파이프 입력이 없는 대체 양식:

`union`[*UnionParameters*] [ `kind=` `inner` | `outer` ] [ `withsource=` *ColumnName*] [ `isfuzzy=` `true` | `false` ] *테이블* [ `,` *테이블*] ...  

## <a name="arguments"></a>인수

::: zone pivot="azuredataexplorer"

* `Table`:
    *  `Events`등과 같은 테이블의 이름, 또는
    *  또는와 같이 괄호로 묶어야 하는 쿼리 식입니다 `(Events | where id==42)` `(cluster("https://help.kusto.windows.net:443").database("Samples").table("*"))` .
    *  와일드 카드를 사용하여 지정한 테이블 집합입니다. 예를 들어는 `E*` 데이터베이스에서 이름이 시작 하는 모든 테이블의 합집합을 형성 합니다 `E` .
* `kind`: 
    * `inner` - 결과에는 모든 입력 테이블에 공통인 열의 하위 집합이 있습니다.
    * `outer`-(기본값). 모든 입력에서 발생 하는 모든 열이 결과에 포함 됩니다. 입력 행에 의해 정의 되지 않은 셀은로 설정 됩니다 `null` .
* `withsource`=*Columnname*: 지정 된 경우 출력에는 각 행을 제공한 원본 테이블을 나타내는 값이 있는 *ColumnName* 이라는 열이 포함 됩니다.
쿼리 (와일드 카드 일치 후)가 둘 이상의 데이터베이스에서 테이블을 참조 하는 경우 (기본 데이터베이스는 항상 계산 됨)이 열의 값에는 데이터베이스와 함께 정규화 된 테이블 이름이 포함 됩니다.
두 개 이상의 클러스터를 참조 하는 경우에는 유사한 __클러스터 및 데이터베이스__ 의 자격도 값에 표시 됩니다. 
* `isfuzzy=``true`  |  `false` : `isfuzzy` 가로 설정 된 경우 `true` 합집합 다리의 유사 항목 확인을 허용 합니다. `Fuzzy`원본 집합에 적용 됩니다 `union` . 즉, 쿼리를 분석 하 고 실행을 준비 하는 동안 통합 원본 집합은 존재 하는 테이블 참조 집합으로 줄어들고 나중에 액세스할 수 있습니다. 이러한 테이블이 하나 이상 발견 되 면 모든 해결 실패는 쿼리 상태 결과 (누락 된 각 참조에 대해 하나)에서 경고를 생성 하지만 쿼리 실행을 방지 하지는 않습니다. 해결 방법이 성공 하지 않은 경우 쿼리는 오류를 반환 합니다.
기본값은 `isfuzzy=` `false`입니다.
* *UnionParameters*: *Name* `=` 행 일치 작업 및 실행 계획의 동작을 제어 하는 이름 *값* 형식의 0 개 이상의 (공백으로 구분 된) 매개 변수입니다. 지원 되는 매개 변수는 다음과 같습니다. 

  |Name           |값                                        |설명                                  |
  |---------------|----------------------------------------------|---------------------------------------------|
  |`hint.concurrency`|*Number*|시스템 힌트 병렬로 실행 해야 하는 연산자의 동시 하위 쿼리 수 `union` 입니다. *기본값*: 클러스터의 단일 노드 (2-16)에 있는 CPU 코어의 양입니다.|
  |`hint.spread`|*Number*|동시 하위 쿼리 실행에서 사용 해야 하는 노드 수를 시스템에 설명 합니다 `union` . *기본값*: 1.|

::: zone-end

::: zone pivot="azuremonitor"

* `Table`:
    *  테이블의 이름 (예:)`Events`
    *  괄호를 포함 해야 하는 쿼리 식입니다 (예:).`(Events | where id==42)`
    *  와일드 카드를 사용하여 지정한 테이블 집합입니다. 예를 들어는 `E*` 데이터베이스에서 이름이 시작 하는 모든 테이블의 합집합을 형성 합니다 `E` .
* `kind`: 
    * `inner` - 결과에는 모든 입력 테이블에 공통인 열의 하위 집합이 있습니다.
    * `outer`-(기본값). 모든 입력에서 발생 하는 모든 열이 결과에 포함 됩니다. 입력 행에 의해 정의 되지 않은 셀은로 설정 됩니다 `null` .
* `withsource`=*Columnname*: 지정 된 경우 출력에는 각 행을 제공한 원본 테이블을 나타내는 값이 있는 *ColumnName* 이라는 열이 포함 됩니다.
쿼리 (와일드 카드 일치 후)가 둘 이상의 데이터베이스에서 테이블을 참조 하는 경우 (기본 데이터베이스는 항상 계산 됨)이 열의 값에는 데이터베이스와 함께 정규화 된 테이블 이름이 포함 됩니다.
마찬가지로, 두 개 이상의 클러스터를 참조 하는 경우 __클러스터 및 데이터베이스__ 의 자격도 값에 표시 됩니다. 
* `isfuzzy=``true`  |  `false` : `isfuzzy` 가로 설정 된 경우 `true` 합집합 다리의 유사 항목 확인을 허용 합니다. `Fuzzy`원본 집합에 적용 됩니다 `union` . 즉, 쿼리를 분석 하 고 실행을 준비 하는 동안 통합 원본 집합은 존재 하는 테이블 참조 집합으로 줄어들고 나중에 액세스할 수 있습니다. 이러한 테이블이 하나 이상 발견 되 면 모든 해결 실패는 쿼리 상태 결과 (누락 된 각 참조에 대해 하나)에서 경고를 생성 하지만 쿼리 실행을 방지 하지는 않습니다. 해결 방법이 성공 하지 않은 경우 쿼리는 오류를 반환 합니다.
기본값은 `isfuzzy=false`입니다.

::: zone-end

## <a name="returns"></a>반환

모든 입력된 테이블에 있는 만큼 행을 가진 테이블입니다.

**참고**

::: zone pivot="azuredataexplorer"

1. `union`[view 키워드](./letstatement.md) 를 사용 하 여 특성을 사용 하는 경우 범위에 [let 문을](./letstatement.md) 포함할 수 있습니다.
2. `union`범위는 [함수](../management/functions.md)를 포함 하지 않습니다. Union 범위에 함수를 포함 하려면 [view 키워드](./letstatement.md) 를 사용 하 여 [let 문을](./letstatement.md) 정의 합니다.
3. `union`입력이 테이블 ( [테이블](../management/tables.md) [형식 식](./tabularexpressionstatements.md)에 대 한 oppose)이 고 `union` 이 다음에 [where 연산자](./whereoperator.md)를 사용 하는 경우 성능 향상을 위해 둘 다를 [찾기](./findoperator.md)로 바꾸는 것이 좋습니다. 연산자에 의해 생성 된 다른 [출력 스키마](./findoperator.md#output-schema) 를 확인 `find` 합니다. 
4. `isfuzzy=true`원본 해결 단계에만 적용 됩니다 `union` . 원본 테이블 집합이 결정 되 면 가능한 추가 쿼리 오류가 표시 되지 않습니다.
5. 를 사용 하는 경우 결과에는 `outer union` 모든 입력에서 발생 하는 모든 열이 포함 됩니다. 각 열에는 이름 및 형식 발생에 대 한 열이 하나씩 있습니다. 즉, 여러 테이블에 열이 표시 되 고 형식이 여러 개인 경우 결과의 각 형식에 해당 하는 열이 포함 됩니다 `union` . 이 열 이름은 ' _ ' 뒤에 원점 열 [형식](./scalar-data-types/index.md)으로 붙습니다.

::: zone-end

::: zone pivot="azuremonitor"

1. `union`[view 키워드](./letstatement.md) 를 사용 하 여 특성을 사용 하는 경우 범위에 [let 문을](./letstatement.md) 포함할 수 있습니다.
2. `union`범위는 함수를 포함 하지 않습니다. Union 범위에 함수를 포함 하려면- [view 키워드](./letstatement.md) 를 사용 하 여 [let 문을](./letstatement.md) 정의 합니다.
3. `union` 입력이 테이블([테이블 형식](./tabularexpressionstatements.md) 식에 대한 oppose)이고 `union`이 다음에 [where 연산자](./whereoperator.md)를 사용 하는 경우 성능 향상을 위해[를 모두 바꿔 보세요.](./findoperator.md) 연산자에 의해 생성 된 다른 [출력 스키마](./findoperator.md#output-schema) 를 적어 두세요 `find` . 
4. `isfuzzy=``true`원본 해상도의 단계에만 적용 됩니다 `union` . 원본 테이블 집합을 확인 한 후에는 가능한 추가 쿼리 오류가 표시 되지 않습니다.
5. 를 사용 하는 경우 결과에는 `outer union` 모든 입력에서 발생 하는 모든 열이 포함 됩니다. 각 열에는 이름 및 형식 발생에 대 한 열이 하나씩 있습니다. 즉, 여러 테이블에 열이 표시 되 고 형식이 여러 개인 경우 결과의 각 형식에 해당 하는 열이 포함 됩니다 `union` . 이 열 이름은 ' _ ' 뒤에 원점 열 [형식](./scalar-data-types/index.md)으로 붙습니다.

::: zone-end


## <a name="example"></a>예제

```kusto
union K* | where * has "Kusto"
```

데이터베이스에서 이름이로 시작 하 고 열에 단어가 포함 된 모든 테이블의 행 `K` `Kusto` 입니다.

## <a name="example"></a>예제

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

쿼리 상태 관찰-반환 되는 경고는 다음과 같습니다.`Failed to resolve entity 'View_3'`

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

쿼리 상태 관찰-반환 되는 경고는 다음과 같습니다.`Failed to resolve entity 'SomeView*'`

**예: 소스 열 형식이 일치 하지 않습니다.**
 
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

`x`에서 `View_1` 접미사를 받았지만 `_long` 이라는 열 `x_long` 이 이미 결과 스키마에 있으므로 열 이름이 중복 제거 되어 새 열이 생성 됩니다.`x_long1`
