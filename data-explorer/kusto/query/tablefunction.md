---
title: table () (범위 함수)-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 테이블 () (범위 함수)에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 0d5f44d621612e90d83a93f2f5831630520d4ba0
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/13/2020
ms.locfileid: "83371743"
---
# <a name="table-scope-function"></a>table () (범위 함수)

Table () 함수는 이름을 형식의 식으로 제공 하 여 테이블을 참조 합니다 `string` .

```kusto
table('StormEvent')
```

**구문**

`table``(` *TableName* [ `,` *DataScope*가는`)`

**인수**

::: zone pivot="azuredataexplorer"

* *TableName*: `string` 참조 되는 테이블의 이름을 제공 하는 형식의 식입니다. 이 식의 값은 함수에 대 한 호출 지점에서 일정 해야 합니다. 즉, 데이터 컨텍스트에 따라 달라질 수 없습니다.

* 거 대 한 *요소:* `string` 이 데이터가 테이블의 유효 [캐시 정책](../management/cachepolicy.md)에 속하는 방식에 따라 데이터에 대 한 테이블 참조를 제한 하는 데 사용할 수 있는 형식의 선택적 매개 변수입니다. 사용 되는 경우 실제 인수는 `string` 다음 값 중 하나를 사용 하는 상수 식 이어야 합니다.

    - `"hotcache"`: 핫 캐시로 분류 된 데이터만 참조 됩니다.
    - `"all"`: 테이블의 모든 데이터를 참조 합니다.
    - `"default"`: 클러스터 `"all"` 관리자가 기본값으로 사용 하도록 설정 된 경우를 제외 하 고는와 동일 합니다 `"hotcache"` .

::: zone-end

::: zone pivot="azuremonitor"

* *TableName*: `string` 참조 되는 테이블의 이름을 제공 하는 형식의 식입니다. 이 식의 값은 함수에 대 한 호출 지점에서 일정 해야 합니다. 즉, 데이터 컨텍스트에 따라 달라질 수 없습니다.

* 거 대 한 *요소:* `string` 이 데이터가 테이블의 유효 캐시 정책에 속하는 방식에 따라 데이터에 대 한 테이블 참조를 제한 하는 데 사용할 수 있는 형식의 선택적 매개 변수입니다. 사용 되는 경우 실제 인수는 `string` 다음 값 중 하나를 사용 하는 상수 식 이어야 합니다.

    - `"hotcache"`: 핫 캐시로 분류 된 데이터만 참조 됩니다.
    - `"all"`: 테이블의 모든 데이터를 참조 합니다.
    - `"default"`:와 동일 합니다 `"all"` .

::: zone-end

## <a name="examples"></a>예

### <a name="use-table-to-access-table-of-the-current-database"></a>Table ()을 사용 하 여 현재 데이터베이스의 테이블에 액세스

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
table('StormEvent') | count
```

|개수|
|---|
|59066|

### <a name="use-table-inside-let-statements"></a>Let 문 내에서 table () 사용

위와 동일한 쿼리는 `tableName` table () 함수로 전달 되는 매개 변수를 받는 인라인 함수 (let 문)를 사용 하도록 다시 작성할 수 있습니다.

<!-- csl: https://help.kusto.windows.net/Samples -->
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

### <a name="use-table-inside-functions"></a>함수 내에 table () 사용

위와 동일한 쿼리는 `tableName` table () 함수로 전달 되는 매개 변수를 받는 함수에서 사용 하도록 다시 작성할 수 있습니다.

```kusto
.create function foo(tableName:string)
{
    table(tableName) | count
};
```

::: zone pivot="azuredataexplorer"

**참고:** 이러한 함수는 로컬 에서만 사용할 수 있고 클러스터 간 쿼리에서는 사용할 수 없습니다.

::: zone-end

### <a name="use-table-with-non-constant-parameter"></a>상수가 아닌 매개 변수와 함께 table () 사용

스칼라 상수 문자열이 아닌 매개 변수는 함수에 대 한 매개 변수로 전달 될 수 없습니다 `table()` .

아래에서는 이러한 경우에 대 한 해결 방법의 예를 제공 합니다.

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
