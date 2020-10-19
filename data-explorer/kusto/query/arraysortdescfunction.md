---
title: array_sort_desc ()-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 array_sort_desc ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: slneimer
ms.service: data-explorer
ms.topic: reference
ms.date: 09/22/2020
ms.openlocfilehash: 83be80a7eb440e73fd19f213839cef7d58ec1512
ms.sourcegitcommit: 62476f682b7812cd9cff7e6958ace5636ee46755
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2020
ms.locfileid: "92169650"
---
# <a name="array_sort_desc"></a>array_sort_desc ()

하나 이상의 배열을 받습니다. 첫 번째 배열을 내림차순으로 정렬 합니다. 나머지 배열을 다시 정렬 된 첫 번째 배열과 일치 하도록 정렬 합니다.

## <a name="syntax"></a>Syntax

`array_sort_desc(`*[,*..., *argumentn*]`)`

`array_sort_desc(`*[,*..., *argumentn*] `,` *nulls_last*`)`

*Nulls_last* 제공 되지 않은 경우의 기본값이 `true` 사용 됩니다.

## <a name="arguments"></a>인수

* *... arrayN*: 입력 배열입니다.
* *nulls_last*: `null` s가 마지막 이어야 하는지 여부를 나타내는 부울입니다.

## <a name="returns"></a>반환

입력에서와 동일한 수의 배열을 반환 하며, 첫 번째 배열은 오름차순으로 정렬 되 고 나머지 배열은 정렬 된 첫 번째 배열과 일치 하도록 정렬 됩니다.

`null` 첫 번째 배열과 길이가 다른 모든 배열에 대해이 반환 됩니다.

배열에 다른 형식의 요소가 포함 되어 있으면 다음 순서로 정렬 됩니다.

* 숫자, `datetime` 및 `timespan` 요소
* 문자열 요소
* Guid 요소
* 다른 모든 요소

## <a name="example-1---sorting-two-arrays"></a>예제 1-두 배열 정렬

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let array1 = dynamic([1,3,4,5,2]);
let array2 = dynamic(["a","b","c","d","e"]);
print array_sort_desc(array1,array2)
```

|`array1_sorted`|`array2_sorted`|
|---|---|
|[5, 4, 3, 2, 1]|["d", "c", "b", "e", "a"]|

> [!Note]
> 함수에 대 한 인수를 기반으로 출력 열 이름이 자동으로 생성 됩니다. 출력 열에 다른 이름을 지정 하려면 다음 구문을 사용 합니다. `... | extend (out1, out2) = array_sort_desc(array1,array2)`

## <a name="example-2---sorting-substrings"></a>예제 2-부분 문자열 정렬

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let Names = "John,Paul,George,Ringo";
let SortedNames = strcat_array(array_sort_desc(split(Names, ",")), ",");
print result = SortedNames
```

|`result`|
|---|
|Ringo, Paul, John, George|

## <a name="example-3---combining-summarize-and-array_sort_desc"></a>예제 3-요약 및 array_sort_desc 결합

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
datatable(command:string, command_time:datetime, user_id:string)
[
    'chmod',   datetime(2019-07-15),   "user1",
    'ls',      datetime(2019-07-02),   "user1",
    'dir',     datetime(2019-07-22),   "user1",
    'mkdir',   datetime(2019-07-14),   "user1",
    'rm',      datetime(2019-07-27),   "user1",
    'pwd',     datetime(2019-07-25),   "user1",
    'rm',      datetime(2019-07-23),   "user2",
    'pwd',     datetime(2019-07-25),   "user2",
]
| summarize timestamps = make_list(command_time), commands = make_list(command) by user_id
| project user_id, commands_in_chronological_order = array_sort_desc(timestamps, commands)[1]
```

|`user_id`|`commands_in_chronological_order`|
|---|---|
|user1|[<br>  "rm",<br>  "pwd",<br>  "dir",<br>  "chmod",<br>  "mkdir",<br>  l<br>]|
|user2|[<br>  "pwd",<br>  rm<br>]|

> [!Note]
> 데이터에 값이 포함 될 수 `null` 있는 경우 [make_list](makelist-aggfunction.md)대신 [make_list_with_nulls](make-list-with-nulls-aggfunction.md) 를 사용 합니다.

## <a name="example-4---controlling-location-of-null-values"></a>예제 4-값 위치 제어 `null`

기본적으로 `null` 값은 정렬 된 배열의 마지막에 배치 됩니다. 그러나의 마지막 인수로 값을 추가 하 여이를 명시적으로 제어할 수 있습니다 `bool` `array_sort_desc()` .

기본 동작을 사용 하는 예제:

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print array_sort_desc(dynamic([null,"blue","yellow","green",null]))
```

|`print_0`|
|---|
|["노란색", "녹색", "blue", null, null]|

기본값이 아닌 동작의 예:

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print array_sort_desc(dynamic([null,"blue","yellow","green",null]), false)
```

|`print_0`|
|---|
|[null, null, "노란색", "녹색", "blue"]|

## <a name="see-also"></a>참고 항목

첫 번째 배열을 오름차순으로 정렬 하려면 [array_sort_asc ()](arraysortascfunction.md)를 사용 합니다.
