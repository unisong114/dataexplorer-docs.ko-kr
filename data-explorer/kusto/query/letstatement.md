---
title: Let 문-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 Let 문을 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: c2e21f0b41f34b469e409109a2586f3e5fd98fa5
ms.sourcegitcommit: 733bde4c6bc422c64752af338b29cd55a5af1f88
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/13/2020
ms.locfileid: "83271471"
---
# <a name="let-statement"></a>Let 문

Let 문이 이름을 식에 바인딩합니다. Let 문이 표시 되는 나머지 범위 (전역 범위 또는 함수 본문 범위)의 경우 해당 이름을 사용 하 여 바인딩된 값을 참조할 수 있습니다. 해당 이름이 이전에 다른 값에 바인딩된 경우 "가장 안쪽의" let 문 바인딩이 사용 됩니다.

Let 문은 잠재적으로 복잡 한 식을 여러 부분으로 나눌 수 있도록 하 고, 각각 let 문을 통해 이름에 바인딩되고, 함께 구성 하는 것을 허용 하므로 모듈화를 개선 하 고 재사용 합니다. 이러한 함수를 사용 하 여 사용자 정의 함수 및 뷰 (결과가 새 테이블과 같은 테이블에 대 한 식)를 만들 수도 있습니다.

Let 문에 의해 바인딩된 이름은 유효한 엔터티 이름 이어야 합니다.

Let 문에 의해 바인딩된 식은 다음과 같을 수 있습니다.
* 스칼라 형식
* 테이블 형식
* 사용자 정의 함수 (람다)

**구문**

`let`*이름* `=` *ScalarExpression*  |  *TabularExpression*  |  *Functiondefinitionexpression*

* *이름*: 바인딩할 이름입니다. 이름은 유효한 엔터티 이름 이어야 하며 엔터티 이름 이스케이프 (예: `["Name with spaces"]` )가 허용 됩니다. 
* *ScalarExpression*: 값이 이름에 바인딩될 스칼라 결과가 포함 된 식입니다. 예를 들어 `let one=1;`을 참조하십시오.
* *TabularExpression*: 값이 이름에 바인딩되는 테이블 형식 결과가 포함 된 식입니다. 예를 들어 `Logs | where Timestamp > ago(1h)`을 참조하십시오.
* *Functiondefinitionexpression*: 이름에 바인딩할 람다 (익명 함수 선언)를 생성 하는 식입니다.
  예를 들어 `let f=(a:int, b:string) { strcat(b, ":", a) }`을 참조하십시오.

람다 식의 구문은 다음과 같습니다.

[ `view` ] `(` [*TabularArguments*] [ `,` ] [*ScalarArguments*] `)` `{` *functionbody*`}`

`TabularArguments`-[*TabularArgName* `:` `(` [*AtrName* `:` *atrtype*] [ `,` ...] `)` ] [`,` ... ] [`,`]

 or:-[*TabularArgName* `:` `(` `*` `)` ]

`ScalarArguments`-[*ArgName* `:` *argtype*] [ `,` ...]

* `view`는 매개 변수가 없는 람다 (인수 없음)에만 나타날 수 있으며, "모든 테이블"이 쿼리 (예:를 사용 하는 경우) 인 경우 바인딩된 이름이 포함 됨을 나타냅니다 `union *` .
* *TabularArguments* 는 공식 테이블 형식 식 인수 목록입니다.
  각 인수에는 다음이 포함 됩니다.
  * *TabularArgName* -형식 테이블 형식 인수의 이름입니다. 그러면 이름이 *Functionbody* 에 표시 될 수 있으며, 람다가 호출 될 때 특정 값에 바인딩됩니다. 
  * 테이블 스키마 정의-해당 유형 (AtrName: AtrType)이 포함 된 특성 목록입니다.
  람다 호출에 사용 되는 테이블 형식 식은 일치 하는 형식을 가진 이러한 모든 특성을 포함 해야 하지만이에 국한 되지 않습니다. 
  ' (*) '를 테이블 형식 식으로 사용할 수 있습니다. 이 경우 람다 호출에 테이블 형식 식을 사용할 수 있으며 람다 식에서 해당 열에 액세스할 수 없습니다.
  모든 테이블 형식 인수는 스칼라 인수 앞에 나와야 합니다.
* *ScalarArguments* 는 공식 스칼라 인수의 목록입니다. 
  각 인수에는 다음이 포함 됩니다.
  * *ArgName* -정식 스칼라 인수의 이름입니다. 그러면 이름이 *Functionbody* 에 표시 될 수 있으며, 람다가 호출 될 때 특정 값에 바인딩됩니다.  
  * *Argtype* -정식 스칼라 인수의 형식입니다. 현재,,,,, `bool` `string` `long` `datetime` `timespan` `real` , 및 형식 `dynamic` 에 대 한 별칭은 람다 인수 형식으로 지원 됩니다.

**여러 개의 중첩 된 let 문**

`;`다음 예제와 같이 여러 let 문을 구분 기호 사이에 구분 기호로 사용할 수 있습니다.
마지막 문은 올바른 쿼리 식 이어야 합니다. 

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

**예**

### <a name="using-let-to-define-constants"></a>Let을 사용 하 여 상수 정의

다음 예에서는 이름을 `x` 스칼라 리터럴에 바인딩한 `1` 다음 테이블 형식 식 문에서 사용 합니다.

```kusto
let x = 1;
range y from x to x step x
```

동일한 예제입니다 .이 경우에는 다음과 같이 개념을 사용 하 여 let 문의 이름을 지정 합니다 `['name']` .

```kusto
let ['x'] = 1;
range y from x to x step x
```

그러나를 사용 하는 또 다른 예에서는 스칼라 값을 사용할 수 있습니다.

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

다음 예제에서는 하나의 문 ( `foo2` )이 다른 ()를 사용 하는 두 개의 let 문을 정의 합니다 `foo1` .

```kusto
let foo1 = (_start:long, _end:long, _step:long) { range x from _start to _end step _step};
let foo2 = (_step:long) { foo1(1, 100, _step)};
foo2(2) | count
// Result: 50
```

### <a name="using-materialize-function"></a>구체화 함수 사용

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
