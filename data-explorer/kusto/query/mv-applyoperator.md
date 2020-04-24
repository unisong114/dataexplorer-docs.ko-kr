---
title: mv 적용 연산자 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기에서 mv 적용 연산에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: f24bf7721707aa1ba3ae9f0aad49b247f08c2498
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81512311"
---
# <a name="mv-apply-operator"></a>mv-apply 연산자

mv-apply 연산자는 입력 테이블의 각 레코드를 하위 테이블로 확장하고 각 하위 테이블에 하위 쿼리를 적용하고 모든 하위 쿼리 결과의 조합을 반환합니다.

`T` 예를 들어 테이블에 값이 숫자 `Metric` 배열인 형식 `dynamic` 열이 `real` 있다고 가정합니다. 다음 쿼리는 각 `Metric` 값에서 가장 큰 두 값을 찾아 해당 하는 레코드를 반환 합니다.

```kusto
T | mv-apply Metric to typeof(real) on (top 2 by Metric desc)
```

일반적으로 mv 적용 연산자는 다음과 같은 처리 단계를 갖는 것으로 생각할 수 있습니다.

1. [mv 확장](./mvexpandoperator.md) 연산자를 사용하여 입력의 각 레코드를 하위 테이블로 확장합니다.
2. 각 하위 테이블에 대한 하위 쿼리를 적용합니다.
3. 확장되지 않는 원본 열의 (필요한 경우 반복된) 값을 포함하는 각 결과 하위 테이블에 0개 이상의 열을 준비합니다.
4. 결과의 공용 구조로 돌아갑니다.

mv 확장 연산자는 다음과 같은 입력을 가져옵니다.

1. 확장할 동적 배열로 평가하는 하나 이상의 식입니다.
   확장된 각 하위 테이블의 레코드 수는 이러한 동적 배열의 각 최대 길이입니다. 여러 식을 지정하지만 해당 배열의 길이가 다른 경우 필요한 경우 null 값이 도입됩니다.

2. 선택적으로 확장 후 식의 값을 할당하는 이름입니다.
   이러한 열은 하위 테이블의 열 이름이 됩니다.
   지정하지 않으면 열의 원래 이름이 사용되거나(식이 열 참조인 경우) 임의의 이름이 사용됩니다(그렇지 않은 경우).

   > [!NOTE]
   > 기본 열 이름을 사용하는 것이 좋습니다.

3. 확장 후 동적 배열의 요소의 데이터 형식입니다.
   이러한 열은 하위 테이블의 열 형식이 됩니다.
   지정되지 않은 경우 `dynamic`가 사용됩니다.

4. 선택적으로 하위 테이블 레코드를 생성한 배열의 요소의 0기반 인덱스를 지정하는 하위 테이블에 추가할 열의 이름입니다.

5. 선택적으로 확장할 배열 요소의 최대 수입니다.

mv 적용 연산자는 [mv 확장](./mvexpandoperator.md) 연산자의 일반화로 생각할 수 있습니다 (사실 하위 쿼리에 프로젝션만 포함하면 전자에 의해 후자를 구현할 수 있습니다.)

**구문**

*T* `|` T `mv-apply` [*항목 인덱스*] *열확장* `on` `(` [*행 제한*] 하위 *쿼리*`)`

*ItemIndex에* 구문이 있는 위치:

`with_itemindex``=` *인덱서열 이름*

*ColumnsToExpand는* 양식의 하나 이상의 요소의 쉼표로 구분된 목록입니다.

【*이름* `=`】 *배열* 식`to` `typeof` `(`[ *형식 이름*`)`]

*행 제한은* 간단합니다:

`limit`*행 제한*

*SubQuery는* 모든 쿼리 문의 동일한 구문을 가합니다.

**인수**

* *ItemIndex*: 사용되는 경우 배열 확장 단계의 일부로 입력에 추가되고 확장 된 값의 0 기반 배열 인덱스를 나타내는 형식의 `long` 열 이름을 나타냅니다.

* *이름*: 사용되는 경우 각 배열 확장 식의 배열 확장 값을 할당하는 이름입니다.
  (지정되지 않은 경우 사용 가능한 경우 열 이름이 사용되거나 *ArrayExpression이* 간단한 열 이름이 아닌 경우 임의의 이름이 생성됩니다.)

* *ArrayExpression*: 값이 `dynamic` 배열 확장되는 형식의 식입니다.
  식이 입력의 열 이름인 경우 입력 열이 입력에서 제거되고 동일한 이름의 새 열(또는 *ColumnName이 지정된 경우)이* 출력에 나타납니다.

* *형식 이름*: 사용하는 경우 `dynamic` *ArrayExpression의* 개별 요소가 사용하는 형식의 이름입니다. 이 형식을 따르지 않는 요소는 null 값으로 대체됩니다.
  (지정되지 않은 `dynamic` 경우 기본적으로 사용됩니다.)

* *RowLimit*: 사용되는 경우 입력의 각 레코드에서 생성할 레코드 수에 대한 제한이 있습니다.
  (지정되지 않은 경우 2147483647이 사용됩니다.)

* *하위 쿼리*: 각 배열 확장 하위 테이블에 적용되는 암시적 테이블 형식 소스가 있는 테이블 형식 쿼리 식입니다.

**참고 사항**

* [mv 확장](./mvexpandoperator.md) 연산자와 달리 mv 적용 연산자는 어레이 확장만 지원합니다. 속성 가방 확장에 대 한 지원이 없습니다.

**예**

## <a name="getting-the-largest-element-from-the-array"></a>배열에서 가장 큰 요소 얻기

```kusto
let _data =
range x from 1 to 8 step 1
| summarize l=make_list(x) by xMod2 = x % 2;
_data
| mv-apply element=l to typeof(long) on 
(
   top 1 by element
)
```

|xMod2|l           |element|
|-----|------------|-------|
|1    |[1, 3, 5, 7]|7      |
|0    |[2, 4, 6, 8]|8      |

## <a name="calculating-sum-of-largest-two-elments-in-an-array"></a>배열에서 가장 큰 두 엘멘의 합계 계산

```kusto
let _data =
range x from 1 to 8 step 1
| summarize l=make_list(x) by xMod2 = x % 2;
_data
| mv-apply l to typeof(long) on
(
   top 2 by l
   | summarize SumOfTop2=sum(l)
)
```

|xMod2|l        |SumOfTop2|
|-----|---------|---------|
|1    |[1,3,5,7]|12       |
|0    |[2,4,6,8]|14       |


## <a name="using-with_itemindex-for-working-with-subset-of-the-array"></a>배열의 하위 집합으로 작업하는 데 사용 `with_itemindex`

```kusto
let _data =
range x from 1 to 10 step 1
| summarize l=make_list(x) by xMod2 = x % 2;
_data
| mv-apply with_itemindex=index element=l to typeof(long) on 
(
   // here you have 'index' column
   where index >= 3
)
| project index, element
```

|인덱스|element|
|---|---|
|3|7|
|4|9|
|3|8|
|4|10|

## <a name="using-mv-apply-operator-to-sort-the-output-of-makelist-aggregate-by-some-key"></a>연산자 를 `mv-apply` 사용하여 `makelist` 일부 키로 집계 출력정렬

```kusto
datatable(command:string, command_time:datetime, user_id:string)
[
    'chmod',        datetime(2019-07-15),   "user1",
    'ls',           datetime(2019-07-02),   "user1",
    'dir',          datetime(2019-07-22),   "user1",
    'mkdir',        datetime(2019-07-14),   "user1",
    'rm',           datetime(2019-07-27),   "user1",
    'pwd',          datetime(2019-07-25),   "user1",
    'rm',           datetime(2019-07-23),   "user2",
    'pwd',          datetime(2019-07-25),   "user2",
]
| summarize commands_details = make_list(pack('command', command, 'command_time', command_time)) by user_id
| mv-apply command_details = commands_details on
(
    order by todatetime(command_details['command_time']) asc
    | summarize make_list(tostring(command_details['command']))
)
| project-away commands_details 
```

|user_id|list_command_details_command|
|---|---|
|user1|[<br>  "ls",<br>  "mkdir",<br>  "chmod",<br>  "디어",<br>  "pwd",<br>  "rm"<br>]|
|user2|[<br>  "rm",<br>  "pwd"<br>]|


**참고 항목**

* [mv 확장](./mvexpandoperator.md) 연산자.