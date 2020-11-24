---
title: Let 문-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 Let 문을 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 08/09/2020
ms.localizationpriority: high
ms.openlocfilehash: c102637adfa1fd0340d28a67b52354956b511ada
ms.sourcegitcommit: 4e811d2f50d41c6e220b4ab1009bb81be08e7d84
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2020
ms.locfileid: "95513316"
---
# <a name="let-statement"></a>Let 문

Let 문이 이름을 식에 바인딩합니다. Let 문이 표시 되는 범위의 나머지 부분에서는이 이름을 사용 하 여 바인딩된 값을 참조할 수 있습니다. Let 문은 전역 범위 또는 함수 본문 범위 내에 있을 수 있습니다.
해당 이름이 이전에 다른 값에 바인딩된 경우 "가장 안쪽의" let 문 바인딩이 사용 됩니다.

문을 사용 하면 복잡 한 식을 여러 부분으로 나눌 수 있으므로 모듈화를 향상 시키고 재사용할 수 있습니다.
각 파트는 let 문을 통해 이름에 바인딩되고 전체를 구성 합니다. 사용자 정의 함수 및 뷰를 만드는 데 사용할 수도 있습니다. 뷰는 결과가 새 테이블과 같은 테이블에 대 한 식입니다.

> [!NOTE]
> Let 문에 의해 바인딩된 이름은 유효한 엔터티 이름 이어야 합니다.

Let 문에 의해 바인딩된 식은 다음과 같을 수 있습니다.
* 스칼라 형식
* 테이블 형식
* 사용자 정의 함수 (람다)

## <a name="syntax"></a>구문

`let`*이름* `=` *ScalarExpression*  |  *TabularExpression*  |  *Functiondefinitionexpression*

|필드  |정의  |예제  |
|---------|---------|---------|
|*이름*   | 바인딩할 이름입니다. 이름은 유효한 엔터티 이름 이어야 합니다.    |와 같은 엔터티 이름 이스케이프를 `["Name with spaces"]` 사용할 수 있습니다.      |
|*ScalarExpression*     |  값이 이름에 바인딩된 스칼라 결과가 포함 된 식입니다.  | `let one=1;`  |
|*TabularExpression*    | 값이 이름에 바인딩된 테이블 형식 결과가 포함 된 식입니다.   | `Logs | where Timestamp > ago(1h)`    |
|*FunctionDefinitionExpression*   | 이름에 바인딩할 무명 함수 선언 인 람다를 생성 하는 식입니다.   |  `let f=(a:int, b:string) { strcat(b, ":", a) }`  |


### <a name="lambda-expressions-syntax"></a>람다 식 구문

[ `view` ] `(` [*TabularArguments*] [ `,` ] [*ScalarArguments*] `)` `{` *functionbody*`}`

`TabularArguments` -[*TabularArgName* `:` `(` [*AtrName* `:` *atrtype*] [ `,` ...] `)` ] [`,` ... ] [`,`]

 또는

 [*TabularArgName* `:` `(` `*` `)`]

`ScalarArguments` -[*ArgName* `:` *argtype*] [ `,` ...]


|필드  |정의  |예제  |
|---------|---------|---------|
| **봅니다** | 인수를 포함 하지 않는 매개 변수가 없는 람다에만 나타날 수 있습니다. "모든 테이블"이 쿼리인 경우 바인딩된 이름이 포함 됨을 나타냅니다. | 예를 들어를 사용 하는 경우 `union *` 입니다.|
| ***TabularArguments** _ | 공식 테이블 형식 식 인수의 목록입니다. 
| 각 테이블 형식 인수에는 다음이 포함 됩니다.||
|<ul><li> _TabularArgName *</li></ul> | 정식 테이블 형식 인수의 이름입니다. 이 이름은 *Functionbody* 에 표시 될 수 있으며 람다를 호출할 때 특정 값에 바인딩됩니다. ||
|<ul><li>테이블 스키마 정의 </li></ul> | 해당 형식이 포함 된 특성 목록| AtrName: AtrType|
| ***ScalarArguments** _ | 정식 스칼라 인수의 목록입니다. 
|각 스칼라 인수에는 다음이 포함 됩니다.||
|<ul><li>_ArgName *</li></ul> | 공식 스칼라 인수의 이름입니다. 이 이름은 *Functionbody* 에 표시 될 수 있으며 람다를 호출할 때 특정 값에 바인딩됩니다.  |
| <ul><li>*ArgType* </li></ul>| 공식 스칼라 인수의 형식입니다. | 현재,,,,, `bool` `string` `long` `datetime` `timespan` `real` , 및 형식 `dynamic` 에 대 한 별칭은 람다 인수 형식으로 지원 됩니다.

> [!NOTE]
>람다 호출에 사용 되는 테이블 형식 식은 일치 하는 형식을 가진 모든 특성을 포함 해야 합니다 (이에 국한 되지 않음).
>
>`(*)` 테이블 형식 식으로 사용할 수 있습니다. 
>
> 모든 테이블 형식 식은 람다 호출에서 사용할 수 있으며 람다 식에서 해당 열에 액세스할 수 없습니다. 
>
> 모든 테이블 형식 인수는 스칼라 인수 앞에 나와야 합니다.

## <a name="multiple-and-nested-let-statements"></a>여러 개의 중첩 된 let 문

다음 예제와 같이 여러 let 문을 세미콜론, `;` , 구분 기호 사이에 사용할 수 있습니다.

> [!NOTE]
> 마지막 문은 올바른 쿼리 식 이어야 합니다. 

```kusto
let start = ago(5h); 
let period = 2h; 
T | where Time > start and Time < start + period | ...
```

중첩 된 let 문이 허용 되며 람다 식 내에서 사용할 수 있습니다.
Let 문과 인수는 함수 본문의 현재 및 내부 범위에서 표시 됩니다.

```kusto
let start_time = ago(5h); 
let end_time = start_time + 2h; 
T | where Time > start_time and Time < end_time | ...
```

## <a name="examples"></a>예

### <a name="use-let-function-to-define-constants"></a>Let 함수를 사용 하 여 상수 정의

다음 예에서는 이름을 `x` 스칼라 리터럴에 바인딩한 `1` 다음 테이블 형식 식 문에서 사용 합니다.

```kusto
let x = 1;
range y from x to x step x
```

이 예제는 이전 예제와 유사 하며, 개념을 사용 하 여 let 문의 이름만 제공 됩니다 `['name']` .

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

### <a name="use-let-statement-with-arguments-for-scalar-calculation"></a>스칼라 계산에 대 한 인수와 함께 let 문 사용

이 예에서는 let 문을 스칼라 계산에 대 한 인수와 함께 사용 합니다. 이 쿼리는 `MultiplyByN` 두 숫자를 곱하는 함수를 정의 합니다.

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

다음 예에서는 입력에서 선행/후행 1 ()을 제거 합니다 `1` .

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


### <a name="use-multiple-let-statements"></a>여러 let 문 사용

이 예제에서는 하나의 문 ( `foo2` )이 다른 ()를 사용 하는 두 개의 let 문을 정의 `foo1` 합니다.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let foo1 = (_start:long, _end:long, _step:long) { range x from _start to _end step _step};
let foo2 = (_step:long) { foo1(1, 100, _step)};
foo2(2) | count
// Result: 50
```

### <a name="use-the-view-keyword-in-a-let-statement"></a>Let 문에서 키워드를 사용 합니다. `view`

이 예에서는 let 문을 키워드와 함께 사용 하는 방법을 보여 줍니다 `view` .

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


### <a name="use-materialize-function"></a>구체화 함수 사용

[`materialize`](materializefunction.md)함수를 사용 하면 쿼리 실행 시 하위 쿼리 결과를 캐시할 수 있습니다. 

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
