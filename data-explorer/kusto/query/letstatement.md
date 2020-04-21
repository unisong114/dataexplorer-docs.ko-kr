---
title: Let 문 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기에서 Let 문을 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 890a6e21400048031e4ebd3df9749b6c47803e71
ms.sourcegitcommit: 653bfb3edf32553c52ef36b339c8b80713a601b0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81524449"
---
# <a name="let-statement"></a>Let 문

명령문에 이름을 바인딩합니다. let 문이 표시되는 나머지 범위(전역 범위 또는 함수 본문 범위)의 경우 이름을 사용하여 바인딩된 값을 참조할 수 있습니다. 해당 이름이 이전에 다른 값에 바인딩된 경우 "가장 안쪽" let 문 바인딩이 사용 됩니다.

문은 잠재적으로 복잡한 식을 여러 부분으로 나누고 각각 let 문을 통해 이름에 바인딩하고 함께 구성할 수 있도록 하여 모듈성과 재사용을 개선할 수 있도록 합니다. 또한 사용자 정의 함수 및 뷰(결과가 새 테이블처럼 보이는 테이블 위에 식)를 만드는 데 사용할 수도 있습니다.

let 문으로 바인딩된 이름은 유효한 엔터티 이름이어야 합니다.

let 문으로 바인딩된 표현식은 다음과 같은 가을일 수 있습니다.
* 스칼라 타입의
* 표형타입의
* 사용자 정의 함수(람다)

**구문**

`let`*이름* `=` 스칼라*표현표표현식* |  *ScalarExpression* | *함수정의표현식*

* *이름*: 바인딩할 이름입니다. 이름은 유효한 엔터티 이름이어야 하며 엔터티 이름 이스케이프(예: `["Name with spaces"]`)가 허용됩니다. 
* *스칼라 표현식*: 값이 이름에 바인딩되는 스칼라 결과가 있는 식입니다. 예: `let one=1;`
* *TabularExpression*: 값이 이름에 바인딩되는 테이블 형식 결과가 있는 식입니다. 예: `Logs | where Timestamp > ago(1h)`
* *FunctionDefinitionExpression*: 이름에 바인딩할 람다(익명 함수 선언)를 생성하는 식입니다.
  예: `let f=(a:int, b:string) { strcat(b, ":", a) }`

Lambda 식에는 다음과 같은 구문이 있습니다.

[`view` `(`[ [*[표*`,`인수][ [[*[스칼라 인수]*`)` `{` *함수바디*`}`

`TabularArguments`- [*[표기아르그네임* `:` `(`[*AtrName* `:` *AtrType*] [ [`,` ... ] `)`] [`,` ... ] [`,`]

 or: - [*[표기아르그네임]* `:` `(` `*` `)`

`ScalarArguments`- [*아르그네임* `:` *아르그타입]*[ [ ]`,`

* `view`매개 변수가 없는 람다(인수가 없는 람다)에만 나타날 수 있으며 "모든 테이블"이 쿼리일 때 바인딩된 `union *`이름이 포함될 것임을 나타냅니다(예: 사용 시).
* *테이블 형식인수는* 공식 테이블 형식 식 인수의 목록입니다.
  각 인수에는 다음이 있습니다.
  * *표ArargName* - 공식 테이블 형식 인수의 이름입니다. 그런 다음 이름은 *FunctionBody에* 나타날 수 있으며 람다를 호출할 때 특정 값에 바인딩됩니다. 
  * 테이블 스키마 정의 - 해당 형식의 특성 목록(AtrName : AtrType)입니다.
  lambda 호출에 사용되는 테이블 형식은 일치하는 형식과 이러한 모든 특성을 가져야 하지만 이에 국한되지 는 않습니다. 
  '(*)'는 표식식으로 사용할 수 있습니다. 이 경우 모든 테이블 형식 식은 lambda 호출에서 사용할 수 있으며 lambda 식에서 해당 열에 액세스할 수 없습니다.
  모든 테이블 형식 인수는 스칼라 인수 앞에 나타나야 합니다.
* *스칼라 인수는* 공식적인 스칼라 인수의 목록입니다. 
  각 인수에는 다음이 있습니다.
  * *ArgName* - 공식 스칼라 인수의 이름입니다. 그런 다음 이름은 *FunctionBody에* 나타날 수 있으며 람다를 호출할 때 특정 값에 바인딩됩니다.  
  * *ArgType* - 형식 스칼라 인수의 유형입니다. 현재 는 다음 형식만 `bool`람다 인수 유형으로 `string` `long` `datetime` `timespan` `real` `dynamic` 지원됩니다.

**다중 및 중첩 let 문**

다음 예제와 같이 `;` 여러 let 문을 구분 기호와 함께 사용할 수 있습니다.
마지막 문은 유효한 쿼리 식이어야 합니다. 

```kusto
let start = ago(5h); 
let period = 2h; 
T | where Time > start and Time < start + period | ...
```

중첩 let 문은 허용되며 람다 식 내에서 사용할 수 있습니다.
함수 본문의 현재 및 내부 범위에 문과 인수를 볼 수 있습니다.

```kusto
let start_time = ago(5h); 
let end_time = start_time + 2h; 
T | where Time > start_time and Time < end_time | ...
```

**예**

### <a name="using-let-to-define-constants"></a>let을 사용하여 상수를 정의합니다.

다음 예제에서는 이름을 `x` 스칼라 리터럴에 `1`바인딩한 다음 테이블 형식 식 문에서 사용합니다.

```kusto
let x = 1;
range y from x to x step x
```

동일한 예이지만이 경우 - let 문의 이름은 개념을 `['name']` 사용하여 제공됩니다.

```kusto
let ['x'] = 1;
range y from x to x step x
```

그러나 scalar 값에 let을 사용하는 또 다른 예는 다음과 같은 것입니다.

```kusto
let n = 10;  // number
let place = "Dallas";  // string
let cutoff = ago(62d); // datetime
Events 
| where timestamp > cutoff 
    and city == place 
| take n
```

### <a name="using-multiple-let-statements"></a>여러 let 문 사용

다음 예제에서는 한 문 ()`foo2`다른 ()를`foo1`사용 하는 두 let 문을 정의 합니다.

```kusto
let foo1 = (_start:long, _end:long, _step:long) { range x from _start to _end step _step};
let foo2 = (_step:long) { foo1(1, 100, _step)};
foo2(2) | count
// Result: 50
```

### <a name="using-materialize-function"></a>구체화 기능 사용

[`materialize`](materializefunction.md)함수를 사용하면 쿼리 실행 시간 동안 하위 쿼리 결과를 캐싱할 수 있습니다. 

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
