---
title: let 문 - Azure Data Explorer
description: 이 문서에서는 Azure Data Explorer의 let 문에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 08/09/2020
ms.localizationpriority: high
ms.openlocfilehash: 7723a3ccd06963596744db9c73f499a882b54c3e
ms.sourcegitcommit: 1530a38181ec92ed1c2c1f3aa2a75f69bd3e9045
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/30/2020
ms.locfileid: "97822783"
---
# <a name="let-statement"></a>Let 문

let 문은 이름을 식에 바인딩합니다. let 문이 표시되는 나머지 범위의 경우 이름을 사용하여 바인딩된 해당 값을 참조할 수 있습니다. let 문은 전역 범위 또는 함수 본문 범위 내에 있을 수 있습니다.
해당 이름이 이전에 다른 값에 바인딩된 경우 "가장 안쪽"의 let 문 바인딩이 사용됩니다.

let 문은 잠재적으로 복잡한 식을 여러 부분으로 분할할 수 있으므로 모듈화 및 재사용을 향상시킵니다.
각 부분은 let 문을 통해 이름에 바인딩되고, 모든 부분이 전체를 구성합니다. 또한 사용자 정의 함수 및 보기를 만드는 데 사용할 수도 있습니다. 보기는 결과가 새 테이블처럼 표시되는 테이블에 대한 식입니다.

> [!NOTE]
> let 문으로 바인딩된 이름은 유효한 엔터티 이름이어야 합니다.

let 문으로 바인딩된 식은 다음과 같을 수 있습니다.
* 스칼라 형식
* 테이블 형식
* 사용자 정의 함수(람다)

## <a name="syntax"></a>Syntax

`let` *Name* `=` *ScalarExpression* | *TabularExpression* | *FunctionDefinitionExpression*

|필드  |정의  |예제  |
|---------|---------|---------|
|*이름*   | 바인딩할 이름입니다. 이름은 유효한 엔터티 이름이어야 합니다.    |엔터티 이름 이스케이프(예: `["Name with spaces"]`)가 허용됩니다.      |
|*ScalarExpression*     |  값이 이름에 바인딩된 스칼라 결과가 포함된 식입니다.  | `let one=1;`  |
|*TabularExpression*    | 값이 이름에 바인딩된 테이블 형식 결과가 포함된 식입니다.   | `Logs | where Timestamp > ago(1h)`    |
|*FunctionDefinitionExpression*   | 이름에 바인딩되는 익명 함수 선언인 람다를 생성하는 식입니다.   |  `let f=(a:int, b:string) { strcat(b, ":", a) }`  |


### <a name="lambda-expressions-syntax"></a>람다 식 구문

[`view`] `(`[*TabularArguments*][`,`][*ScalarArguments*]`)` `{` *FunctionBody* `}`

`TabularArguments` - [*TabularArgName* `:` `(`[*AtrName* `:` *AtrType*] [`,` ... ]`)`] [`,` ... ][`,`]

 또는

 [*TabularArgName* `:` `(` `*` `)`] - 변수와 알 수 없는 열이 포함된 테이블 형식의 식을 나타냅니다.

`ScalarArguments` - [*ArgName* `:` *ArgType*] [`,` ... ]


|필드  |정의  |예제  |
|---------|---------|---------|
| **view** | 인수가 포함되지 않은 매개 변수가 없는 let 문에만 표시될 수 있습니다. 'view' 키워드를 사용하는 경우 테이블/뷰의 와일드카드를 선택할 수 있는 `union` 연산자를 사용하는 쿼리에 let 문이 포함됩니다. |  |
| **_TabularArguments_* _ | 형식 테이블 형식 식 인수의 목록입니다. 
| 각 테이블 형식 인수에는 다음이 포함됩니다.||
|<ul><li> _TabularArgName*</li></ul> | 형식 테이블 형식 인수의 이름입니다. 이 이름은 *FunctionBody* 에 표시될 수 있으며, 람다를 호출할 때 특정 값에 바인딩됩니다. ||
|<ul><li>테이블 스키마 정의 </li></ul> | 해당 형식이 포함된 특성의 목록입니다.| AtrName : AtrType|
| ***ScalarArguments** _ | 형식 스칼라 인수의 목록입니다. 
|각 스칼라 인수에는 다음이 포함됩니다.||
|<ul><li>_ArgName*</li></ul> | 형식 스칼라 인수의 이름입니다. 이 이름은 *FunctionBody* 에 표시될 수 있으며, 람다를 호출할 때 특정 값에 바인딩됩니다.  |
| <ul><li>*ArgType* </li></ul>| 형식 스칼라 인수의 형식입니다. | 현재 `bool`, `string`, `long`, `datetime`, `timespan`, `real` 및 `dynamic` 형식(및 이러한 형식에 대한 별칭)만 람다 인수 형식으로 지원됩니다.

> [!NOTE]
>람다 호출에 사용되는 테이블 형식 식에는 일치하는 형식의 모든 특성이 포함되어야 합니다(이에 국한되지 않음).
>
>`(*)`는 테이블 형식 식으로 사용할 수 있습니다. 
>
> 테이블 형식 식은 람다 호출에 사용할 수 있지만, 람다 식에서 해당 열에 액세스할 수 없습니다. 
>
> 모든 테이블 형식 인수는 스칼라 인수 앞에 나와야 합니다.

## <a name="multiple-and-nested-let-statements"></a>다중 및 중첩 let 문

다중 let 문은 다음 예제와 같이 명령문 사이에 세미콜론(`;`) 구분 기호를 사용할 수 있습니다.

> [!NOTE]
> 마지막 명령문은 올바른 쿼리 식이어야 합니다. 

```kusto
let start = ago(5h); 
let period = 2h; 
T | where Time > start and Time < start + period | ...
```

중첩 let 문은 허용되며 람다 식 내에서 사용할 수 있습니다.
let 문과 인수는 함수 본문의 현재 및 내부 범위에서 표시됩니다.

```kusto
let start_time = ago(5h); 
let end_time = start_time + 2h; 
T | where Time > start_time and Time < end_time | ...
```

## <a name="examples"></a>예제

### <a name="use-let-function-to-define-constants"></a>let 함수를 사용하여 상수 정의

다음 예제에서는 `x` 이름을 `1` 스칼라 리터럴에 바인딩한 다음, 이를 테이블 형식 식 명령문에서 사용합니다.

```kusto
let x = 1;
range y from x to x step x
```

다음 예제는 이전 예제와 비슷하며 `['name']` 표기법을 사용하여 let 문의 이름만 지정합니다.

```kusto
let ['x'] = 1;
range y from x to x step x
```

### <a name="use-let-for-scalar-values"></a>스칼라 값에 let 사용

```kusto
let n = 10;  // number
let place = "Dallas";  // string
let cutoff = ago(62d); // datetime
Events 
| where timestamp > cutoff 
    and city == place 
| take n
```

### <a name="use-let-statement-with-arguments-for-scalar-calculation"></a>스칼라 계산에 인수가 포함된 let 문 사용

다음 예제에서는 인수가 포함된 let 문을 스칼라 계산에 사용합니다. 쿼리는 두 숫자를 곱하는 `MultiplyByN` 함수를 정의합니다.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let MultiplyByN = (val:long, n:long) { val * n };
range x from 1 to 5 step 1 
| extend result = MultiplyByN(x, 5)
```

|x|result|
|---|---|
|1|5|
|2|10|
|3|15|
|4|20|
|5|25|

다음 예제에서는 입력에서 선행/후행 항목(`1`)을 제거합니다.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let TrimOnes = (s:string) { trim("1", s) };
range x from 10 to 15 step 1 
| extend result = TrimOnes(tostring(x))
```

|x|result|
|---|---|
|10|0|
|11||
|12|2|
|13|3|
|14|4|
|15|5|


### <a name="use-multiple-let-statements"></a>다중 let 문 사용

다음 예제에서는 한 명령문(`foo2`)에서 다른 명령문(`foo1`)을 사용하는 두 개의 let 문을 정의합니다.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let foo1 = (_start:long, _end:long, _step:long) { range x from _start to _end step _step};
let foo2 = (_step:long) { foo1(1, 100, _step)};
foo2(2) | count
// Result: 50
```

### <a name="use-the-view-keyword-in-a-let-statement"></a>let 문에 `view` 키워드 사용

다음 예제에서는 `view` 키워드가 포함된 let 문을 사용하는 방법을 보여 줍니다.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let Range10 = view () { range MyColumn from 1 to 10 step 1 };
let Range20 = view () { range MyColumn from 1 to 20 step 1 };
search MyColumn == 5
```

|$table|MyColumn|
|---|---|
|Range10|5|
|Range20|5|


### <a name="use-materialize-function"></a>materialize 함수 사용

[`materialize`](materializefunction.md) 함수를 사용하면 쿼리 실행 시간 동안 하위 쿼리 결과를 캐시할 수 있습니다. 

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let totalPagesPerDay = PageViews
| summarize by Page, Day = startofday(Timestamp)
| summarize count() by Day;
let materializedScope = PageViews
| summarize by Page, Day = startofday(Timestamp);
let cachedResult = materialize(materializedScope);
cachedResult
| project Page, Day1 = Day
| join kind = inner
(
    cachedResult
    | project Page, Day2 = Day
)
on Page
| where Day2 > Day1
| summarize count() by Day1, Day2
| join kind = inner
    totalPagesPerDay
on $left.Day1 == $right.Day
| project Day1, Day2, Percentage = count_*100.0/count_1
```

|Day1|Day2|백분율|
|---|---|---|
|2016-05-01 00:00:00.0000000|2016-05-02 00:00:00.0000000|34.0645725975255|
|2016-05-01 00:00:00.0000000|2016-05-03 00:00:00.0000000|16.618368960101|
|2016-05-02 00:00:00.0000000|2016-05-03 00:00:00.0000000|14.6291376489636|
