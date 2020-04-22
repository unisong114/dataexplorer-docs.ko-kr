---
title: 테이블() (범위 함수) - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 테이블(범위 함수)에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 233baebaf51cdc8b07cdd32cec9bd10a54695c1c
ms.sourcegitcommit: 01eb9aaf1df2ebd5002eb7ea7367a9ef85dc4f5d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81766163"
---
# <a name="table-scope-function"></a>표() (범위 함수)

table() 함수는 해당 이름을 형식의 `string`식으로 제공하여 테이블을 참조합니다.

```kusto
table('StormEvent')
```

**구문**

`table``(` *테이블* 이름`,` [ *데이터 스코프*]`)`

**인수**

::: zone pivot="azuredataexplorer"

* *테이블 이름*: 참조되는 테이블의 이름을 제공하는 형식의 `string` 식입니다. 이 식의 값은 함수를 호출할 때 일정해야 합니다(즉, 데이터 컨텍스트에 따라 달라질 수 있음).

* *DataScope*: 이 데이터가 `string` 테이블의 유효 [캐시 정책에](../management/cachepolicy.md)속하는 방식에 따라 테이블 참조를 데이터에 제한하는 데 사용할 수 있는 형식의 선택적 매개 변수입니다. 실제 인수는 다음과 같은 가능한 `string` 값 중 하나를 갖는 상수 식이어야 합니다.

    - `"hotcache"`: 핫 캐시로 분류된 데이터만 참조됩니다.
    - `"all"`: 테이블의 모든 데이터가 참조됩니다.
    - `"default"`: 클러스터가 클러스터 `"all"`관리자가 기본값으로 사용하도록 `"hotcache"` 설정된 경우를 제외하고 는 와 동일합니다.

::: zone-end

::: zone pivot="azuremonitor"

* *테이블 이름*: 참조되는 테이블의 이름을 제공하는 형식의 `string` 식입니다. 이 식의 값은 함수를 호출할 때 일정해야 합니다(즉, 데이터 컨텍스트에 따라 달라질 수 있음).

* *DataScope*: 이 데이터가 `string` 테이블의 유효 캐시 정책에 속하는 방식에 따라 테이블 참조를 데이터에 제한하는 데 사용할 수 있는 형식의 선택적 매개 변수입니다. 실제 인수는 다음과 같은 가능한 `string` 값 중 하나를 갖는 상수 식이어야 합니다.

    - `"hotcache"`: 핫 캐시로 분류된 데이터만 참조됩니다.
    - `"all"`: 테이블의 모든 데이터가 참조됩니다.
    - `"default"`: 이것은 `"all"`와 동일합니다.

::: zone-end

## <a name="examples"></a>예

### <a name="use-table-to-access-table-of-the-current-database"></a>테이블()를 사용하여 현재 데이터베이스의 테이블에 액세스

```kusto
table('StormEvent') | count
```

|개수|
|---|
|59066|

### <a name="use-table-inside-let-statements"></a>let 문 내부에 테이블() 사용

table() 함수로 전달되는 매개 변수를 `tableName` 수신하는 인라인 함수(let statement)를 사용하기 위해 위와 동일한 쿼리를 다시 작성할 수 있습니다.

```kusto
let foo = (tableName:string)
{
    table(tableName) | count
};
foo('help')
```

|개수|
|---|
|59066|

### <a name="use-table-inside-functions"></a>함수 내부테이블() 사용

table() 함수로 전달되는 매개 변수를 `tableName` 수신하는 함수에서 위와 동일한 쿼리를 다시 작성할 수 있습니다.

```kusto
.create function foo(tableName:string)
{
    table(tableName) | count
};
```

::: zone pivot="azuredataexplorer"

**참고:** 이러한 함수는 클러스터 간 쿼리가 아닌 로컬에서만 사용할 수 있습니다.

::: zone-end

### <a name="use-table-with-non-constant-parameter"></a>비상 매개 변수가 있는 테이블() 사용

스칼라 상수 문자열이 아닌 매개 변수는 `table()` 함수에 매개 변수로 전달할 수 없습니다.

아래에서 이러한 경우 해결 방법을 예로 들어 보겠습니다.

```kusto
let T1 = print x=1;
let T2 = print x=2;
let _choose = (_selector:string)
{
    union
    (T1 | where _selector == 'T1'),
    (T2 | where _selector == 'T2')
};
_choose('T2')

```

|x|
|---|
|2|
