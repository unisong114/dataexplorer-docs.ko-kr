---
title: mv-적용 연산자-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 mv 적용 연산자에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 8fd83615de466c238a590273b228c118e2cd1b46
ms.sourcegitcommit: 9fe6e34ef3321390ee4e366819ebc9b132b3e03f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/01/2020
ms.locfileid: "84257843"
---
# <a name="mv-apply-operator"></a>mv-apply 연산자

각 레코드에 하위 쿼리를 적용 하 고 모든 하위 쿼리 결과의 합집합을 반환 합니다.

예를 들어 테이블에 `T` `Metric` 값이 숫자 배열인 형식의 열이 있다고 가정 합니다 `dynamic` `real` . 다음 쿼리는 각 값에서 가장 큰 값 두 개를 찾고 `Metric` 이러한 값에 해당 하는 레코드를 반환 합니다.

```kusto
T | mv-apply Metric to typeof(real) on 
(
   top 2 by Metric desc
)
```

연산자에는 `mv-apply` 다음과 같은 처리 단계가 있습니다.

1. 연산자를 사용 하 여 [`mv-expand`](./mvexpandoperator.md) 입력의 각 레코드를 파일당로 확장 합니다.
1. 각 파일당에 대 한 하위 쿼리를 적용 합니다.
1. 결과 하위 테이블이 0 개 이상의 열을 추가 합니다. 이러한 열은 확장 되지 않고 필요한 경우 반복 되는 원본 열의 값을 포함 합니다.
1. 결과의 합집합을 반환 합니다.

`mv-expand`연산자는 다음 입력을 가져옵니다.

1. 확장할 동적 배열로 계산 되는 하나 이상의 식입니다.
   확장 된 각 하위 문서의 레코드 수는 각 동적 배열의 최대 길이입니다. 여러 식이 지정 되 고 해당 배열의 길이가 다른 경우 Null 값이 추가 됩니다.

1. 필요에 따라 확장 후 식의 값을 할당 하는 이름입니다.
   이러한 이름은 파일당에서 열 이름이 됩니다.
   지정 하지 않으면 식이 열 참조일 때 열의 원래 이름이 사용 됩니다. 그렇지 않으면 임의의 이름을 사용 합니다. 

   > [!NOTE]
   > 기본 열 이름을 사용 하는 것이 좋습니다.

1. 확장 후 해당 동적 배열의 요소에 대 한 데이터 형식입니다.
   파일당에 있는 열의 열 형식이 됩니다.
   지정되지 않은 경우 `dynamic`가 사용됩니다.

1. 필요에 따라 하위 테이블이 레코드를 생성 한 배열의 요소에 대 한 0 기반 인덱스를 지정 하는 파일당에 추가할 열의 이름입니다.

1. 선택적으로 확장할 최대 배열 요소 수입니다.

연산자는 `mv-apply` 연산자의 일반화로 간주할 수 있습니다 [`mv-expand`](./mvexpandoperator.md) . 즉, 하위 쿼리에 프로젝션이만 포함 된 경우에는 후자를 이전에 구현할 수 있습니다.

**구문**

*T* `|` `mv-apply` [*itemindex*] *columnstoexpand* [*rowlimit*] `on` `(` *하위 쿼리*`)`

여기서 *Itemindex* 의 구문은 다음과 같습니다.

`with_itemindex``=` *Indexcolumnname*

*Columnstoexpand* 는 다음과 같은 폼의 요소 중 하나 이상에 대 한 쉼표로 구분 된 목록입니다.

[*Name* `=` ] *Arrayexpression* [ `to` `typeof` `(` *Typename* `)` ]

*Rowlimit* 는 다음과 같이 간단 합니다.

`limit`*Rowlimit*

및 *하위* 쿼리에는 동일한 쿼리 문 구문이 있습니다.

**인수**

* *Itemindex*: 사용 하는 경우 `long` 배열 확장 단계의 일부로 입력에 추가 되는 형식의 열 이름을 나타내며 확장 된 값의 0부터 기반으로 하는 배열 인덱스를 나타냅니다.

* *이름*: 사용 하는 경우 배열 확장 된 각 식의 배열 확장 값을 할당할 이름입니다.
  지정 하지 않으면 사용 가능한 경우 열 이름이 사용 됩니다.
  *Arrayexpression* 이 단순한 열 이름이 아닌 경우 임의의 이름이 생성 됩니다.

* *Arrayexpression*: `dynamic` 해당 값이 배열 확장 될 형식의 식입니다.
  식이 입력의 열 이름인 경우 입력에서 입력 열이 제거 되 고 이름이 같은 새 열 (또는 지정 된 경우 *ColumnName* )이 출력에 표시 됩니다.

* *Typename*: 사용 하는 경우 `dynamic` 배열 *arrayexpression* 의 개별 요소에 사용 되는 형식의 이름입니다. 이 형식을 따르지 않는 요소는 null 값으로 대체 됩니다.
  지정 하지 않으면 `dynamic` 기본적으로가 사용 됩니다.

* *Rowlimit*: 사용 되는 경우 입력의 각 레코드에서 생성할 레코드 수에 대 한 제한입니다.
  지정 하지 않으면 2147483647이 사용 됩니다.

* *하위 쿼리*: 각 배열 확장 하위 테이블에 적용 되는 암시적 테이블 형식 소스를 포함 하는 테이블 형식 쿼리 식입니다.

**참고 사항**

* 연산자와 달리 [`mv-expand`](./mvexpandoperator.md) 연산자는 `mv-apply` 배열 확장만 지원 합니다. 속성 모음 확장은 지원 되지 않습니다.

**예**

## <a name="getting-the-largest-element-from-the-array"></a>배열에서 가장 큰 요소 가져오기

<!-- csl: https://help.kusto.windows.net/Samples -->
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

|`xMod2`|l           |element|
|-----|------------|-------|
|1    |[1, 3, 5, 7]|7      |
|0    |[2, 4, 6, 8]|8      |

## <a name="calculating-the-sum-of-the-largest-two-elements-in-an-array"></a>배열에서 가장 큰 두 요소의 합계 계산

<!-- csl: https://help.kusto.windows.net/Samples -->
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

|`xMod2`|l        |SumOfTop2|
|-----|---------|---------|
|1    |[1, 3, 5, 7]|12       |
|0    |[2, 4, 6, 8]|14       |


## <a name="using-with_itemindex-for-working-with-a-subset-of-the-array"></a>`with_itemindex`배열의 하위 집합 작업에 사용

<!-- csl: https://help.kusto.windows.net/Samples -->
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

## <a name="using-the-mv-apply-operator-to-sort-the-output-of-makelist-aggregate-by-some-key"></a>연산자를 사용 하 여 `mv-apply` 집계의 출력을 `makelist` 일부 키로 정렬

<!-- csl: https://help.kusto.windows.net/Samples -->
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

|`user_id`|`list_command_details_command`|
|---|---|
|user1|[<br>  "ls",<br>  "mkdir",<br>  "chmod",<br>  "dir",<br>  "pwd",<br>  rm<br>]|
|user2|[<br>  "rm",<br>  pwd<br>]|


**참고 항목**

* [mv-expand](./mvexpandoperator.md) 연산자.
