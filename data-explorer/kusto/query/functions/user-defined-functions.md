---
title: 사용자 정의 함수 - Azure Data Explorer | Microsoft Docs
description: 이 문서에서는 Azure Data Explorer의 사용자 정의 함수에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/12/2020
ms.localizationpriority: high
ms.openlocfilehash: 92627b3325a7a2ba8e2e4d58a82ebf6db3977221
ms.sourcegitcommit: f49e581d9156e57459bc69c94838d886c166449e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2020
ms.locfileid: "95512879"
---
# <a name="user-defined-functions"></a>사용자 정의 함수

**사용자 정의 함수** 는 다시 사용할 수 있는 하위 쿼리로, 쿼리 자체의 일부로 정의(**임시 함수**)하거나 데이터베이스 메타데이터의 일부로 유지(**저장 함수**)할 수 있습니다. 사용자 정의 함수는 **이름** 을 통해 호출되고, 0개 이상의 **입력 인수**(스칼라 또는 테이블 형식)가 제공되며, 함수 **본문** 에 따라 단일 값(스칼라 또는 테이블 형식)을 생성합니다.

사용자 정의 함수는 다음 두 가지 범주 중 하나에 속합니다.

* 스칼라 함수 
* 테이블 형식 함수 

함수의 입력 인수 및 출력에 따라 함수가 스칼라인지 아니면 테이블 형식인지가 결정되며, 함수 형식에 따라 함수 사용 방법이 설정됩니다. 

## <a name="scalar-function"></a>스칼라 함수

* 입력 인수가 없거나 모든 입력 인수가 스칼라 값
* 단일 스칼라 값 생성
* 스칼라 식이 허용되는 경우에 사용 가능
* 함수가 정의된 행 컨텍스트에서만 사용 가능
* 액세스 가능한 스키마에 있는 테이블(및 보기)만 참조 가능

## <a name="tabular-function"></a>테이블 형식 함수

* 하나 이상의 테이블 형식 입력 인수와 0개 이상의 스칼라 입력 인수 허용
* 그리고/또는 단일 테이블 형식 값 생성

## <a name="function-names"></a>함수 이름

유효한 사용자 정의 함수 이름은 다른 엔터티와 동일한 [식별자 명명 규칙](../schema-entities/entity-names.md#identifier-naming-rules)을 따라야 합니다.

또한 이름은 해당하는 정의 범위 내에서 고유해야 합니다.

> [!NOTE]
> 저장 함수와 테이블의 이름이 같으면 테이블/함수 이름을 쿼리할 때 저장 함수가 재정의됩니다.

## <a name="input-arguments"></a>입력 인수

유효한 사용자 정의 함수는 다음 규칙을 따릅니다.

* 사용자 정의 함수에는 0개 이상의 입력 인수로 구성된 강력한 형식의 목록이 있습니다.
* 입력 인수에는 이름, 형식 및 [기본값](#default-values)(스칼라 인수의 경우)이 포함되어 있습니다.
* 입력 인수의 이름이 식별자입니다.
* 입력 인수의 형식은 스칼라 데이터 형식 또는 테이블 형식 스키마 중 하나입니다.

구문적으로 입력 인수 목록은 쉼표로 구분된 인수 정의 목록이며 괄호로 묶여 있습니다. 각 인수 정의는 다음과 같이 지정됩니다.

```
ArgName:ArgType [= ArgDefaultValue]
```
 테이블 형식 인수의 경우 *ArgType* 은 테이블 정의와 동일한 구문을 사용하며(괄호와 열 이름/형식 쌍의 목록), "모든 테이블 형식 스키마"를 나타내는 독립 `(*)`를 추가로 지원합니다.

다음은 그 예입니다. 

|Syntax                        |입력 인수 목록 설명                                 |
|------------------------------|-----------------------------------------------------------------|
|`()`                          |인수 없음|
|`(s:string)`                  |`string` 형식의 값을 사용하는 단일 스칼라 인수 `s`|
|`(a:long, b:bool=true)`       |두 개의 스칼라 인수. 두 번째 인수는 기본값이 있음    |
|`(T1:(*), T2(r:real), b:bool)`|세 개의 인수(그 중 2개는 테이블 형식 인수이고 나머지 하나는 스칼라 인수)  |

> [!NOTE]
> 테이블 형식 입력 인수와 스칼라 입력 인수를 모두 사용하는 경우 모든 테이블 형식 입력 인수를 스칼라 입력 인수 앞에 배치하세요.

## <a name="examples"></a>예제

스칼라 함수:

```kusto
let Add7 = (arg0:long = 5) { arg0 + 7 };
range x from 1 to 10 step 1
| extend x_plus_7 = Add7(x), five_plus_seven = Add7()
```

인수를 사용하지 않는 테이블 형식 함수:

```kusto
let tenNumbers = () { range x from 1 to 10 step 1};
tenNumbers
| extend x_plus_7 = x + 7
```

테이블 형식 입력과 스칼라 입력을 모두 사용하는 테이블 형식 함수:

```kusto
let MyFilter = (T:(x:long), v:long) {
  T | where x >= v
};
MyFilter((range x from 1 to 10 step 1), 9)
```

|x|
|---|
|9|
|10|

열을 지정하지 않고 테이블 형식 입력을 사용하는 테이블 형식 함수.
모든 테이블을 함수에 전달할 수 있으며, 함수 내에서 테이블 열을 참조할 수 없습니다.

```kusto
let MyDistinct = (T:(*)) {
  T | distinct *
};
MyDistinct((range x from 1 to 3 step 1))
```

|x|
|---|
|1|
|2|
|3|

## <a name="declaring-user-defined-functions"></a>사용자 정의 함수 선언

사용자 정의 함수를 선언하여 다음을 제공합니다.

* 함수 **이름**
* 함수 **스키마**(허용되는 매개 변수. 있는 경우)
* 함수 **본문**

> [!Note]
> 오버로드 함수는 지원되지 않습니다. 동일한 이름과 서로 다른 입력 스키마를 사용하여 여러 함수를 만들 수 없습니다.

> [!TIP]
> 람다 함수는 이름이 없으며 [let 문](../letstatement.md)을 사용하여 이름에 바인딩됩니다. 따라서 사용자 정의 저장 함수로 간주할 수 있습니다.
> 예제: 두 인수(`s`라는 `string` 및 `i`라는 `long`)를 허용하는 람다 함수에 대한 선언을 예로 들 수 있습니다. 이 함수는 첫 번째 인수(숫자로 변환한 후)와 두 번째 인수의 곱을 반환합니다. 람다는 이름 `f`에 바인딩됩니다.

```kusto
let f=(s:string, i:long) {
    tolong(s) * i
};
```

함수 **본문** 에는 다음이 포함됩니다.

* 함수의 반환 값(스칼라 또는 테이블 형식 값)을 제공하는 정확히 하나의 식
* 함수 본문의 범위에 해당하는 [let 문](../letstatement.md)의 수(0개 이상). 이 값을 지정하는 경우 let 문은 함수의 반환 값을 정의하는 식 앞에 와야 합니다.
* 함수에서 사용하는 쿼리 매개 변수를 선언하는 [쿼리 매개 변수 명령문](../queryparametersstatement.md)의 수(0개 이상). 이 값을 지정하는 경우 이 명령문은 함수의 반환 값을 정의하는 식 앞에 와야 합니다.

> [!NOTE]
> "최상위 수준" 쿼리에서 지원되는 다른 종류의 [쿼리 명령문](../statements.md)은 함수 본문 내에서 지원되지 않습니다.

### <a name="examples-of-user-defined-functions"></a>사용자 정의 함수 예제 

**let 문을 사용하는 사용자 정의 함수**

다음 예제에서는 3개의 let 문을 사용하는 사용자 정의 함수(람다)에 이름 `Test`를 바인딩합니다. 출력은 `70`입니다.

```kusto
let Test1 = (id: int) {
  let Test2 = 10;
  let Test3 = 10 + Test2 + id;
  let Test4 = (arg: int) {
      let Test5 = 20;
      Test2 + Test3 + Test5 + arg
  };
  Test4(10)
};
range x from 1 to Test1(10) step 1
| count
```

**매개 변수의 기본값을 정의하는 사용자 정의 함수**

다음 예제에서는 세 가지 인수를 허용하는 함수를 보여줍니다. 두 번째 및 세 번째 인수는 기본값이 있으며 반드시 호출 사이트에 있을 필요가 없습니다.

```kusto
let f = (a:long, b:string = "b.default", c:long = 0) {
  strcat(a, "-", b, "-", c)
};
print f(12, c=7) // Returns "12-b.default-7"
```

## <a name="invoking-a-user-defined-function"></a>사용자 정의 함수 호출

인수를 사용하지 않는 사용자 정의 함수는 괄호 안에 이름 또는 이름과 빈 인수 목록을 사용하여 호출할 수 있습니다.

예:

```kusto
// Bind the identifier a to a user-defined function (lambda) that takes
// no arguments and returns a constant of type long:
let a=(){123};
// Invoke the function in two equivalent ways:
range x from 1 to 10 step 1
| extend y = x * a, z = x * a() 
```

```kusto
// Bind the identifier T to a user-defined function (lambda) that takes
// no arguments and returns a random two-by-two table:
let T=(){
  range x from 1 to 2 step 1
  | project x1 = rand(), x2 = rand()
};
// Invoke the function in two equivalent ways:
// (Note that the second invocation must be itself wrapped in
// an additional set of parentheses, as the union operator
// differentiates between "plain" names and expressions)
union T, (T())
```

하나 이상의 스칼라 인수를 사용하는 사용자 정의 함수는 괄호 안에 테이블 이름과 구체적인 인수 목록을 사용하여 호출할 수 있습니다.

```kusto
let f=(a:string, b:string) {
  strcat(a, " (la la la)", b)
};
print f("hello", "world")
```

하나 이상의 테이블 형식 인수(그리고 임의의 개수의 스칼라 인수)를 사용하는 사용자 정의 함수는 괄호 안에 테이블 이름과 구체적인 인수 목록을 사용하여 호출할 수 있습니다.

```kusto
let MyFilter = (T:(x:long), v:long) {
  T | where x >= v 
};
MyFilter((range x from 1 to 10 step 1), 9)
```

`invoke` 연산자를 사용하여 하나 이상의 테이블 인수를 사용하고 테이블을 반환하는 사용자 정의 함수를 호출할 수도 있습니다. 이 함수는 함수에 대한 첫 번째 구체적인 테이블 인수가 `invoke` 연산자의 원본인 경우에 유용합니다.

```kusto
let append_to_column_a=(T:(a:string), what:string) {
    T | extend a=strcat(a, " ", what)
};
datatable (a:string) ["sad", "really", "sad"]
| invoke append_to_column_a(":-)")
```

## <a name="default-values"></a>기본값

다음과 같은 조건에서는 함수가 일부 매개 변수의 기본값을 제공할 수도 있습니다.

* 기본값은 스칼라 매개 변수에만 제공됩니다.
* 기본값은 항상 리터럴(상수)입니다. 임의의 계산일 수 없습니다.
* 기본값이 없는 매개 변수는 기본값이 있는 매개 변수보다 항상 우선합니다.
* 호출자는 기본값이 없는 모든 매개 변수의 값을 함수 선언과 동일한 순서로 정렬된 상태로 제공해야 합니다.
* 호출자는 기본값이 있는 매개 변수의 값을 제공할 필요가 없지만, 제공해도 됩니다.
* 호출자는 매개 변수의 순서와 일치하지 않는 순서로 인수를 제공해도 됩니다. 이 경우 인수의 이름을 지정해야 합니다.

다음 예제에서는 두 개의 동일한 레코드가 있는 테이블을 반환합니다. `f`를 처음 호출할 때 인수는 완전히 "스크램블"되므로 각 인수의 이름이 명시적으로 지정됩니다.

```kusto
let f = (a:long, b:string = "b.default", c:long = 0) {
  strcat(a, "-", b, "-", c)
};
union
  (print x=f(c=7, a=12)), // "12-b.default-7"
  (print x=f(12, c=7))    // "12-b.default-7"
```

|x|
|---|
|12-b.default-7|
|12-b.default-7|

## <a name="view-functions"></a>함수 보기

인수를 사용하지 않고 테이블 형식 식을 반환하는 사용자 정의 함수는 **보기** 로 표시할 수 있습니다. 사용자 정의 함수를 보기로 표시하는 것은 와일드카드 테이블 이름 확인이 수행될 때마다 함수가 테이블처럼 작동한다는 뜻입니다.
다음 예제에서는 두 개의 사용자 정의 함수 `T_view` 및 `T_notview`를 보여주고, 첫 번째 함수만 `union`에서 와일드카드 참조로 확인되는 이유를 보여줍니다.

```kusto
let T_view = view () { print x=1 };
let T_notview = () { print x=2 };
union T*
```

## <a name="restrictions"></a>제한

다음 제한 사항이 적용됩니다.

* 사용자 정의 함수는 함수가 호출되는 행 컨텍스트에 따라 달라지는 [toscalar()](../toscalarfunction.md) 호출 정보를 전달할 수 없습니다.
* 테이블 형식 식을 반환하는 사용자 정의 함수는 행 컨텍스트에 따라 달라지는 인수를 사용하여 호출할 수 없습니다.
* 하나 이상의 테이블 형식 입력을 사용하는 함수는 원격 클러스터에서 호출할 수 없습니다.
* 스칼라 함수는 원격 클러스터에서 호출할 수 없습니다.

사용자 정의 함수가 스칼라 함수로만 구성되고 `toscalar()`를 사용하지 않는 경우에만 행 컨텍스트에 따라 달라지는 인수를 사용하여 사용자 정의 함수를 호출할 수 있습니다.

**제한의 예 1**

```kusto
// Supported:
// f is a scalar function that doesn't reference any tabular expression
let Table1 = datatable(xdate:datetime)[datetime(1970-01-01)];
let Table2 = datatable(Column:long)[1235];
let f = (hours:long) { now() + hours*1h };
Table2 | where Column != 123 | project d = f(10)

// Supported:
// f is a scalar function that references the tabular expression Table1,
// but is invoked with no reference to the current row context f(10):
let Table1 = datatable(xdate:datetime)[datetime(1970-01-01)];
let Table2 = datatable(Column:long)[1235];
let f = (hours:long) { toscalar(Table1 | summarize min(xdate) - hours*1h) };
Table2 | where Column != 123 | project d = f(10)

// Not supported:
// f is a scalar function that references the tabular expression Table1,
// and is invoked with a reference to the current row context f(Column):
let Table1 = datatable(xdate:datetime)[datetime(1970-01-01)];
let Table2 = datatable(Column:long)[1235];
let f = (hours:long) { toscalar(Table1 | summarize min(xdate) - hours*1h) };
Table2 | where Column != 123 | project d = f(Column)
```

**제한의 예 2**

```kusto
// Not supported:
// f is a tabular function that is invoked in a context
// that expects a scalar value.
let Table1 = datatable(xdate:datetime)[datetime(1970-01-01)];
let Table2 = datatable(Column:long)[1235];
let f = (hours:long) { range x from 1 to hours step 1 | summarize make_list(x) };
Table2 | where Column != 123 | project d = f(Column)
```

## <a name="features-that-are-currently-unsupported-by-user-defined-functions"></a>사용자 정의 함수에서 현재 지원되지 않는 기능

참고로, 다음은 사용자 정의 함수에 대해 많이 요청되지만 아직은 지원되지 않는 기능입니다.

1.  함수 오버로드: 현재는 함수를 오버로드할 수 없습니다. 즉, 이름은 같고 입력 스키마가 다른 여러 함수를 만들 수 없습니다.

2.  기본값: 함수에 대한 스칼라 매개 변수의 기본값은 스칼라 리터럴(상수)이어야 합니다. 또한 저장 함수는 `dynamic` 형식의 기본값을 가질 수 없습니다.
