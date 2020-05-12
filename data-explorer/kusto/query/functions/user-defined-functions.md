---
title: 사용자 정의 함수-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 사용자 정의 함수에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/12/2020
ms.openlocfilehash: e9e199631d57f3fd3be8d438e6b0cdbf9bdd4266
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83227362"
---
# <a name="user-defined-functions"></a>사용자 정의 함수

**사용자 정의 함수** 는 쿼리 자체의 일부로 정의 되거나 (임시**함수**) 데이터베이스 메타 데이터 (**저장 된 함수**)의 일부로 지속 될 수 있는 재사용 가능한 하위 쿼리입니다. 사용자 정의 함수는 **이름을**통해 호출 되 고 0 개 이상의 **입력 인수** (스칼라 또는 테이블 형식)와 함께 제공 되며 함수 **본문**을 기반으로 하 여 단일 값 (스칼라 또는 테이블 형식)을 생성할 수 있습니다.

사용자 정의 함수는 다음 두 가지 범주 중 하나에 속합니다.

* 스칼라 함수 
* 테이블 형식 함수 

함수의 입력 인수 및 출력은 스칼라 또는 테이블 형식 인지 여부를 확인 하 여이를 사용 하는 방법을 설정 합니다. 

## <a name="scalar-function"></a>스칼라 함수

* 에 입력 인수가 없거나 모든 입력 인수가 스칼라 값인 경우
* 단일 스칼라 값을 생성 합니다.
* 스칼라 식이 허용 되는 모든 위치에서 사용할 수 있습니다.
* 정의 된 행 컨텍스트에서만 사용할 수 있습니다.
* 액세스 가능한 스키마에 있는 테이블 (및 뷰)만 참조할 수 있습니다.

## <a name="tabular-function"></a>테이블 형식 함수

* 하나 이상의 테이블 형식 입력 인수와 0 개 이상의 스칼라 입력 인수 및/또는를 허용 합니다.
* 단일 테이블 형식 값을 생성 합니다.

## <a name="function-names"></a>함수 이름

유효한 사용자 정의 함수 이름은 다른 엔터티와 동일한 [식별자 명명 규칙](../schema-entities/entity-names.md#identifier-naming-rules) 을 따라야 합니다.

또한 이름은 해당 정의의 범위 내에서 고유 해야 합니다.

> [!NOTE]
> 함수 오버 로드는 지원 되지 않습니다. 같은 이름을 사용 하 여 여러 함수를 정의할 수 없습니다.

## <a name="input-arguments"></a>입력 인수

유효한 사용자 정의 함수는 다음 규칙을 따릅니다.

* 사용자 정의 함수에는 0 개 이상의 입력 인수에 대 한 강력한 형식의 목록이 있습니다.
* 입력 인수에는 이름, 형식 및 (스칼라 인수의 경우) [기본값이](#default-values)있습니다.
* 입력 인수의 이름이 식별자입니다.
* 입력 인수의 형식은 스칼라 데이터 형식 또는 테이블 형식 스키마 중 하나입니다.

구문적으로 입력 인수 목록은 괄호 안에 래핑된 인수 정의의 쉼표로 구분 된 목록입니다. 각 인수 정의는로 지정 됩니다.

```
ArgName:ArgType [= ArgDefaultValue]
```
 테이블 형식 인수의 경우 *Argtype* 은 테이블 정의 (괄호 및 열 이름/형식 쌍의 목록)와 동일한 구문을 사용 하며, `(*)` "모든 테이블 형식 스키마"를 나타내는 독립를 추가로 지원 합니다.

예:

|구문                        |입력 인수 목록 설명                                 |
|------------------------------|-----------------------------------------------------------------|
|`()`                          |인수 없음|
|`(s:string)`                  |`s`형식의 값을 가져오는 단일 스칼라 인수를 호출 합니다.`string`|
|`(a:long, b:bool=true)`       |두 번째 스칼라 인수입니다. 여기에는 기본값이 있습니다.    |
|`(T1:(*), T2(r:real), b:bool)`|세 개의 인수 (테이블 형식 인수 및 스칼라 인수 1 개)  |

> [!NOTE]
> 테이블 형식 입력 인수와 스칼라 입력 인수를 모두 사용 하는 경우 모든 테이블 형식 입력 인수 앞에 스칼라 입력 인수를 추가 합니다.

## <a name="examples"></a>예

스칼라 함수:

```kusto
let Add7 = (arg0:long = 5) { arg0 + 7 };
range x from 1 to 10 step 1
| extend x_plus_7 = Add7(x), five_plus_seven = Add7()
```

인수를 취하지 않는 테이블 형식 함수:

```kusto
let tenNumbers = () { range x from 1 to 10 step 1};
tenNumbers
| extend x_plus_7 = x + 7
```

테이블 형식 입력과 스칼라 입력을 모두 가져오는 테이블 형식 함수:

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

열을 지정 하지 않고 테이블 형식 입력을 사용 하는 테이블 형식 함수입니다.
모든 테이블을 함수에 전달할 수 있으며 함수 내에서 테이블 열을 참조할 수 없습니다.

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

사용자 정의 함수의 선언은 다음을 제공 합니다.

* 함수 **이름**
* 함수 **스키마** (허용 하는 매개 변수 (있는 경우)
* 함수 **본문**

> [!Note]
> 오버 로드 함수는 지원 되지 않습니다. 동일한 이름과 다른 입력 스키마를 사용 하 여 여러 함수를 만들 수는 없습니다.

> [!TIP]
> 람다 함수에는 이름이 없으며 [let 문을](../letstatement.md)사용 하 여 이름에 바인딩됩니다. 따라서 사용자 정의 저장 함수로 간주할 수 있습니다.
> 예: 두 개의 인수를 허용 하는 람다 함수에 대 한 선언 (호출 되는 `string` `s` 및 `long` `i` )입니다. 첫 번째 (숫자를 숫자로 변환한 후)와 두 번째의 곱을 반환 합니다. 람다는 이름에 바인딩됩니다 `f` .

```kusto
let f=(s:string, i:long) {
    tolong(s) * i
};
```

함수 **본문** 에는 다음이 포함 됩니다.

* 함수의 반환 값 (스칼라 또는 테이블 형식 값)을 제공 하는 정확히 하나의 식입니다.
* 해당 범위가 함수 본문의 모든 숫자 (0 개 이상)의 [let 문](../letstatement.md)입니다. 지정 하는 경우 let 문은 함수의 반환 값을 정의 하는 식 앞에와 야 합니다.
* 함수에서 사용 하는 쿼리 매개 변수를 선언 하는 [쿼리 매개 변수 문의](../queryparametersstatement.md)수 (0 개 이상) 지정 된 경우 함수 반환 값을 정의 하는 식 앞에와 야 합니다.

> [!NOTE]
> "최상위 수준" 쿼리에서 지원 되는 다른 종류의 [쿼리 문은](../statements.md) 함수 본문 내에서 지원 되지 않습니다.

### <a name="examples-of-user-defined-functions"></a>사용자 정의 함수의 예 

**Let 문을 사용 하는 사용자 정의 함수**

다음 예에서는 `Test` 세 개의 let 문을 사용 하는 람다 (사용자 정의 함수)에 이름을 바인딩합니다. 출력은 `70` 다음과 같습니다.

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

**매개 변수의 기본값을 정의 하는 사용자 정의 함수**

다음 예제에서는 세 개의 인수를 허용 하는 함수를 보여 줍니다. 후자는 기본값을 가지 며 호출 사이트에 있을 필요가 없습니다.

```kusto
let f = (a:long, b:string = "b.default", c:long = 0) {
  strcat(a, "-", b, "-", c)
};
print f(12, c=7) // Returns "12-b.default-7"
```

## <a name="invoking-a-user-defined-function"></a>사용자 정의 함수 호출

인수를 사용 하지 않는 사용자 정의 함수는 이름 또는 이름으로 호출 하거나 괄호 안에 빈 인수 목록을 사용 하 여 호출할 수 있습니다.

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

하나 이상의 스칼라 인수를 사용 하는 사용자 정의 함수는 괄호로 묶인 테이블 이름과 구체적인 인수 목록을 사용 하 여 호출할 수 있습니다.

```kusto
let f=(a:string, b:string) {
  strcat(a, " (la la la)", b)
};
print f("hello", "world")
```

하나 이상의 테이블 인수 (및 임의 개수의 스칼라 인수)를 사용 하는 사용자 정의 함수는 괄호로 묶인 테이블 이름과 구체적인 인수 목록을 사용 하 여 호출할 수 있습니다.

```kusto
let MyFilter = (T:(x:long), v:long) {
  T | where x >= v 
};
MyFilter((range x from 1 to 10 step 1), 9)
```

연산자를 사용 `invoke` 하 여 하나 이상의 테이블 인수를 사용 하 고 테이블을 반환 하는 사용자 정의 함수를 호출할 수도 있습니다. 이 함수는 함수에 대 한 첫 번째 구체적 테이블 인수가 연산자의 원본인 경우에 유용 합니다 `invoke` .

```kusto
let append_to_column_a=(T:(a:string), what:string) {
    T | extend a=strcat(a, " ", what)
};
datatable (a:string) ["sad", "really", "sad"]
| invoke append_to_column_a(":-)")
```

## <a name="default-values"></a>기본값

함수는 다음 조건에서 일부 매개 변수에 대 한 기본값을 제공할 수 있습니다.

* 스칼라 매개 변수에 대해서만 기본값을 제공할 수 있습니다.
* 기본값은 항상 리터럴 (상수)입니다. 임의의 계산 일 수 없습니다.
* 기본값이 없는 매개 변수는 항상 기본값을 갖는 매개 변수 앞에와 야 합니다.
* 호출자는 함수 선언과 동일한 순서로 정렬 된 기본값이 없는 모든 매개 변수의 값을 제공 해야 합니다.
* 호출자는 기본값을 사용 하 여 매개 변수의 값을 제공할 필요가 없지만 그렇게 할 수 있습니다.
* 호출자는 매개 변수의 순서와 일치 하지 않는 순서로 인수를 제공할 수 있습니다. 그렇다면 인수의 이름을로 바꿔야 합니다.

다음 예에서는 두 개의 동일한 레코드가 있는 테이블을 반환 합니다. 의 첫 번째 호출에서 `f` 인수는 완전히 "스크램블" 되므로 각 항목에는 명시적으로 이름이 지정 됩니다.

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
|12-b. 기본값-7|
|12-b. 기본값-7|

## <a name="view-functions"></a>함수 보기

인수를 사용 하지 않고 테이블 형식 식을 반환 하는 사용자 정의 함수는 **뷰로**표시 될 수 있습니다. 사용자 정의 함수를 뷰로 표시 하는 것은 와일드 카드 테이블 이름 확인이 완료 될 때마다 함수가 테이블 처럼 동작 함을 의미 합니다.
다음 예에서는 및 라는 두 개의 사용자 정의 함수를 보여 주고 첫 번째 함수를의 `T_view` `T_notview` 와일드 카드 참조로 확인 하는 방법을 보여 줍니다 `union` .

```kusto
let T_view = view () { print x=1 };
let T_notview = () { print x=2 };
union T*
```

## <a name="restrictions"></a>제한

다음 제한 사항이 적용됩니다.

* 사용자 정의 함수는 함수가 호출 되는 행 컨텍스트에 종속 된 [toscalar ()](../toscalarfunction.md) 호출 정보를 전달할 수 없습니다.
* 테이블 형식 식을 반환 하는 사용자 정의 함수는 행 컨텍스트에 따라 달라 지는 인수를 사용 하 여 호출 됩니다.
* 하나 이상의 테이블 형식 입력을 가져오는 함수는 원격 클러스터에서 호출할 수 없습니다.
* 원격 클러스터에서는 스칼라 함수를 호출할 수 없습니다.

사용자 정의 함수는 사용자 정의 함수를 스칼라 함수로만 구성 하 고를 사용 하지 않는 경우에만 행 컨텍스트에 따라 달라 지는 인수를 사용 하 여 호출할 수 있습니다 `toscalar()` .

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

**제한 2의 예**

```kusto
// Not supported:
// f is a tabular function that is invoked in a context
// that expects a scalar value.
let Table1 = datatable(xdate:datetime)[datetime(1970-01-01)];
let Table2 = datatable(Column:long)[1235];
let f = (hours:long) { range x from 1 to hours step 1 | summarize make_list(x) };
Table2 | where Column != 123 | project d = f(Column)
```
