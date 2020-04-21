---
title: 사용자 정의 함수 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 사용자 정의 함수에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/12/2020
ms.openlocfilehash: e8372be303b1540e1421f226ed0fd0fe74d44545
ms.sourcegitcommit: c4aea69fafa9d9fbb814764eebbb0ae93fa87897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/17/2020
ms.locfileid: "81610217"
---
# <a name="user-defined-functions"></a>사용자 정의 함수

Kusto는 다음과 같은 사용자 정의 함수를 지원합니다.
* 쿼리 자체의**일부(임시 함수)** 
* 데이터베이스 메타 데이터의 일부로 지속적인 방식으로 저장 **(저장된 함수)**

사용자 정의 함수에는 다음이 있습니다.
* 이름:
    * [식별자 이름 지정 규칙을](../schema-entities/entity-names.md#identifier-naming-rules) 따라야 합니다.
    * 형식 사양을 사용 하 고 정의 범위에서 고유 해야 합니다.
* 강력한 형식의 입력 매개 변수 목록:
    * 스칼라 또는 표식식일 수 있습니다.
    * Scalar 매개 변수는 함수의 호출자가 매개 변수에 대한 값을 제공하지 않을 때 암시적으로 사용되는 기본값으로 제공 될 수 있습니다 [(아래 기본값](#default-values)참조).
* 스칼라 또는 테이블 형식일 수 있는 강력한 형식의 반환 값

함수의 입력 및 출력에 따라 함수를 사용할 수 있는 방법과 위치를 결정합니다.

* **스칼라 기능**: 
    * 입력이 없거나 스칼라 입력만 있는 함수이며 스칼라 출력을 생성합니다.
    * 스칼라 표현식이 허용되는 모든 곳에서 사용할 수 있습니다.
    * 정의된 행 컨텍스트만 사용할 수 있습니다.
    * 액세스 가능한 스키마에 있는 테이블(및 보기)만 참조할 수 있습니다.

예:

```kusto
let Add7 = (arg0:long = 5) { arg0 + 7 };
range x from 1 to 10 step 1
| extend x_plus_7 = Add7(x), five_plus_seven = Add7()
```

* **표 형식 함수**: 
    * 입력이 없거나 하나 이상의 테이블 형식 입력이 있는 함수이며 테이블 형식 출력을 생성합니다.
    * 테이블 형식 식이 허용되는 모든 곳에서 사용할 수 있습니다. 

> [!NOTE]
> 모든 테이블 형식 매개 변수는 스칼라 매개 변수 앞에 나타나야 합니다.

인수를 하지 않는 테이블 형식 함수의 예:

```kusto
let tenNumbers = () { range x from 1 to 10 step 1};
tenNumbers
| extend x_plus_7 = x + 7
```

테이블 입력 및 스칼라 입력을 사용하는 테이블 형식 함수의 예:

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

열이 지정되지 않은 테이블 형식 입력을 사용하는 테이블 형식 함수의 예입니다. 모든 테이블을 함수에 전달할 수 있으며 함수 내에서 테이블의 열을 참조할 수 없습니다.

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

사용자 정의 함수의 선언은 다음을 제공합니다.

* 함수 **이름**
* 함수 **스키마(수락하는** 매개 변수(있는 경우)
* 기능 **본문**

> [!Note]
> 오버로드 함수는 지원되지 않습니다. 예를 들어 이름이 같고 입력 스키마가 다른 여러 함수를 만들 수 없습니다.

> [!TIP]
> Lambda 함수에는 이름이 없으며 [let 문을](../letstatement.md)사용하여 이름에 바인딩됩니다. 따라서 사용자 정의 된 저장 함수로 간주될 수 있습니다.
> 예: 두 인수(호출 `string` 및 `s` `long` 호출)를 `i`허용하는 람다 함수에 대한 선언입니다. 첫 번째(숫자로 변환한 후)와 두 번째 의 곱을 반환합니다. 람다의 이름은 다음과 같은 `f`이름으로 바인딩됩니다.

```kusto
let f=(s:string, i:long) {
    tolong(s) * i
};
```

함수 **본문에는** 다음이 포함됩니다.

* 함수의 반환 값(스칼라 또는 테이블 형식 값)을 제공하는 정확히 하나의 식입니다.
* [let 문(함수](../letstatement.md)본문의 범위)의 모든 숫자(0 이상)입니다. 지정한 경우 let 문은 함수의 반환 값을 정의하는 식 앞에 있어야 합니다.
* 함수에서 사용하는 [쿼리 매개 변수를](../queryparametersstatement.md)선언하는 쿼리 매개 변수 문(0개 이상)의 모든 수입니다. 지정한 경우 함수의 반환 값을 정의하는 식 앞에 와야 합니다.

> [!NOTE]
> 쿼리 "최상위 수준"에서 지원되는 다른 종류의 [쿼리 문은](../statements.md) 함수 본문 내에서 지원되지 않습니다.

### <a name="examples-of-user-defined-functions"></a>사용자 정의 함수의 예 

**let 문을 사용하는 사용자 정의 함수**

다음 예제에서는 세 `Test` let 문을 사용하는 사용자 정의 함수(람다)에 이름을 바인딩합니다. 출력은 `70`다음과 같은 것입니다.

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

다음 예제에서는 세 개의 인수를 허용하는 함수를 보여 주며 있습니다. 후자의 두 기본값은 호출 사이트에 있을 필요가 없습니다.

```kusto
let f = (a:long, b:string = "b.default", c:long = 0) {
  strcat(a, "-", b, "-", c)
};
print f(12, c=7) // Returns "12-b.default-7"
```

## <a name="invoking-a-user-defined-function"></a>사용자 정의 함수 호출

인수를 사용 하지 않는 사용자 정의 함수는 해당 이름 또는 괄호 안에 빈 인수 목록으로 이름을 호출할 수 있습니다.

예제:

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

괄호 안에 테이블 이름과 구체적인 인수 목록을 사용하여 하나 이상의 스칼라 인수를 사용하는 사용자 정의 함수를 호출할 수 있습니다.

```kusto
let f=(a:string, b:string) {
  strcat(a, " (la la la)", b)
};
print f("hello", "world")
```

하나 이상의 테이블 인수(및 임의의 수의 스칼라 인수)를 사용하는 사용자 정의 함수는 괄호 안에 테이블 이름과 구체적인 인수 목록을 사용하여 호출할 수 있습니다.

```kusto
let MyFilter = (T:(x:long), v:long) {
  T | where x >= v 
};
MyFilter((range x from 1 to 10 step 1), 9)
```

연산자를 `invoke` 사용하여 하나 이상의 테이블 인수를 사용하고 테이블을 반환하는 사용자 정의 함수를 호출할 수도 있습니다. 함수에 대한 첫 번째 콘크리트 테이블 인수가 `invoke` 연산자의 소스인 경우에 유용합니다.

```kusto
let append_to_column_a=(T:(a:string), what:string) {
    T | extend a=strcat(a, " ", what)
};
datatable (a:string) ["sad", "really", "sad"]
| invoke append_to_column_a(":-)")
```

## <a name="default-values"></a>기본값

함수는 다음 조건에서 일부 매개 변수에 기본값을 제공할 수 있습니다.

* 스칼라 매개변수에 대해서만 기본값을 제공할 수 있습니다.
* 기본값은 항상 리터럴(상수)입니다. 임의 계산이 될 수 없습니다.
* 기본값이 없는 매개 변수는 항상 기본값이 있는 매개 변수 앞에 옵니다.
* 호출자는 함수 선언과 동일한 순서로 정렬된 기본값이 없는 모든 매개 변수의 값을 제공해야 합니다.
* 호출자는 기본값을 가진 매개 변수에 대한 값을 제공할 필요는 없지만 그렇게 할 수 있습니다.
* 호출자는 매개 변수의 순서와 일치하지 않는 순서로 인수를 제공할 수 있습니다. 그렇다면 인수의 이름을 지정해야 합니다.

다음 예제는 두 개의 동일한 레코드가 있는 테이블을 반환합니다. `f`의 첫 번째 호출에서 인수는 완전히 "스크램블"되므로 각 인수에는 명시적으로 이름이 지정됩니다.

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

인수를 사용 하지 않고 테이블 형식 식을 반환 하는 사용자 정의 함수를 **뷰로**표시할 수 있습니다. 사용자 정의 함수를 뷰로 표시하면 와일드카드 테이블 이름 확인이 완료될 때마다 함수가 테이블처럼 작동합니다.
다음 예제에서는 두 개의 사용자 `T_view` 정의 `T_notview`함수를 보여 주며, 첫 번째 함수만 다음의 와일드카드 참조에서 `union`해결되는 방법을 보여 주십을 보여 주십습니다.

```kusto
let T_view = view () { print x=1 };
let T_notview = () { print x=2 };
union T*
```

## <a name="user-defined-functions-usage-restrictions"></a>사용자 정의 기능 사용 제한

다음 제한 사항이 적용됩니다.

* 사용자 정의 함수는 함수가 호출되는 행 컨텍스트에 종속된 [toscalar()](../toscalarfunction.md) 호출 정보를 전달할 수 없습니다.
* 테이블 형식 식을 반환 하는 사용자 정의 함수행 컨텍스트에 따라 달라 집니다 인수로 호출할 수 없습니다.
* 하나 이상의 테이블 형식 입력을 취하는 함수는 원격 클러스터에서 호출할 수 없습니다.
* 원격 클러스터에서는 스칼라 함수를 호출할 수 없습니다.

사용자 정의 함수가 행 컨텍스트에 따라 달라지는 인수로 호출될 수 있는 유일한 장소는 사용자 정의 함수가 스칼라 함수로만 구성되고 사용하지 `toscalar()`않는 경우입니다.

**제한 1의 예**

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
