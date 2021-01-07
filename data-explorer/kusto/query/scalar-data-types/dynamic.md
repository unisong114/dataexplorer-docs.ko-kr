---
title: dynamic 데이터 형식 - Azure Data Explorer | Microsoft Docs
description: 이 문서에서는 Azure Data Explorer의 dynamic 데이터 형식에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 07/09/2020
ms.localizationpriority: high
ms.openlocfilehash: 2ef75f2249466a9796fb0313186546bd9088ff07
ms.sourcegitcommit: 28f18c3500992fd7dcfd95dffe2bae0161a22ca3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/28/2020
ms.locfileid: "97792934"
---
# <a name="the-dynamic-data-type"></a>dynamic 데이터 형식

`dynamic` 스칼라 데이터 형식은 배열 및 속성 모음뿐만 아니라 아래 목록에 있는 다른 스칼라 데이터 형식의 모든 값도 사용할 수 있다는 점에서 특별합니다. 특히 `dynamic` 값은 다음과 같을 수 있습니다.

* null
* 기본 스칼라 데이터 형식(`bool`, `datetime`, `guid`, `int`, `long`, `real`, `string` 및 `timespan`)의 값
* 0부터 시작하는 인덱싱을 통해 0개 이상의 값이 포함된 `dynamic` 값의 배열
* 고유한 `string` 값을 `dynamic` 값에 매핑하는 속성 모음.
  속성 모음에는 고유한 `string` 값으로 인덱싱된 0개 이상의 이러한 매핑("슬롯"이라고 함)이 있습니다. 슬롯은 순서대로 정렬되지 않습니다.

> [!NOTE]
> * `dynamic` 형식의 값은 1MB(2^20)로 제한됩니다.
> * `dynamic` 형식은 JSON과 비슷하게 표시되지만 JSON에 없으므로 JSON 모델에서 표현하지 않는 값(예: `long`, `real`, `datetime`, `timespan` 및 `guid`)을 포함할 수 있습니다.
>   따라서 `dynamic` 값을 JSON 표현으로 직렬화할 때 JSON에서 표현할 수 없는 값은 `string` 값으로 직렬화됩니다. 반대로 Kusto는 문자열을 강력한 형식의 값으로 구문 분석할 수 있는 경우 이 방식으로 구문 분석합니다.
>   이는 `datetime`, `real`, `long` 및 `guid` 형식에 적용됩니다. 
>   JSON 개체 모델에 대한 자세한 내용은 [json.org](https://json.org/)를 참조하세요.
> * Kusto는 속성 모음에서 이름-값 매핑의 순서를 유지하려고 시도하지 않으므로 유지할 순서를 가정할 수 없습니다. 예를 들어 매핑 집합이 동일한 두 개의 속성 모음이 `string` 값으로 표현되면 완전히 다른 결과를 생성할 수 있습니다.

## <a name="dynamic-literals"></a>동적 리터럴

`dynamic` 형식의 리터럴은 다음과 같습니다.

`dynamic(` *값* `)`

*값* 은 다음과 같을 수 있습니다.

* `null`: 이 경우 리터럴은 null 동적 값(`dynamic(null)`)을 나타냅니다.
* 다른 스칼라 데이터 형식 리터럴: 이 경우 리터럴은 "내부" 형식의 `dynamic` 리터럴을 나타냅니다. 예를 들어 `dynamic(4)`은 long 스칼라 데이터 형식의 값(4)이 포함된 동적 값입니다.
* dynamic 또는 다른 리터럴의 배열: `[` *ListOfValues* `]`입니다. 예를 들어 `dynamic([1, 2, "hello"])`는 두 개의 `long` 값 및 하나의 `string` 값이라는 세 개의 요소에 대한 동적 배열입니다.
* 속성 모음: `{` *이름* `=` *값* ... `}`입니다. 예를 들어 `dynamic({"a":1, "b":{"a":2}})`은 두 개의 슬롯(`a` 및 `b`)이 있는 속성 모음이며, 두 번째 슬롯은 또 다른 속성 모음입니다.

```kusto
print o=dynamic({"a":123, "b":"hello", "c":[1,2,3], "d":{}})
| extend a=o.a, b=o.b, c=o.c, d=o.d
```

편의상 쿼리 텍스트 자체에 표시되는 `dynamic` 리터럴은 `datetime`, `timespan`, `real`, `long`, `guid`, `bool` 및 `dynamic` 형식의 다른 Kusto 리터럴도 포함할 수 있습니다.
문자열을 구문 분석하는 경우(예: `parse_json` 함수를 사용하거나 데이터를 수집하는 경우) JSON을 통한 이 확장은 사용할 수 없지만, 다음 작업을 수행할 수 있습니다.

```kusto
print d=dynamic({"a": datetime(1970-05-11)})
```

JSON 인코딩 규칙을 따르는 `string` 값을 `dynamic` 값으로 구문 분석하려면 `parse_json` 함수를 사용합니다. 다음은 그 예입니다. 

* `parse_json('[43, 21, 65]')` - 숫자의 배열
* `parse_json('{"name":"Alan", "age":21, "address":{"street":432,"postcode":"JLK32P"}}')` - 사전
* `parse_json('21')` - 숫자를 포함하는 동적 형식의 단일 값
* `parse_json('"21"')` - 문자열을 포함하는 동적 형식의 단일 값
* `parse_json('{"a":123, "b":"hello", "c":[1,2,3], "d":{}}')` - 위의 예제에서 `o`와 같은 값을 제공합니다.

> [!NOTE]
> JSON에서는 JavaScript와 달리 문자열 및 속성 모음 이름 주위에 큰따옴표(`"`) 문자를 사용해야 합니다.
> 따라서 일반적으로 작은따옴표(`'`) 문자를 사용하여 JSON 인코딩 문자열 리터럴을 묶는 것이 더 쉽습니다.
  
다음 예제에서는 `dynamic` 열(및 `datetime` 열)이 포함되는 테이블을 정의한 다음, 단일 레코드로 수집하는 방법을 보여 줍니다. 또한 CSV 파일에서 JSON 문자열을 인코딩하는 방법도 보여 줍니다.

```kusto
// dynamic is just like any other type:
.create table Logs (Timestamp:datetime, Trace:dynamic)

// Everything between the "[" and "]" is parsed as a CSV line would be:
// 1. Since the JSON string includes double-quotes and commas (two characters
//    that have a special meaning in CSV), we must CSV-quote the entire second field.
// 2. CSV-quoting means adding double-quotes (") at the immediate beginning and end
//    of the field (no spaces allowed before the first double-quote or after the second
//    double-quote!)
// 3. CSV-quoting also means doubling-up every instance of a double-quotes within
//    the contents.
.ingest inline into table Logs
  [2015-01-01,"{""EventType"":""Demo"", ""EventValue"":""Double-quote love!""}"]
```

|타임스탬프                   | 추적                                                 |
|----------------------------|-------------------------------------------------------|
|2015-01-01 00:00:00.0000000 | {"EventType":"Demo","EventValue":"Double-quote love!"}|

## <a name="dynamic-object-accessors"></a>동적 개체 접근자

사전에 대한 아래 첨자를 지정하려면 점 표기법(`dict.key`) 또는 대괄호 표기법(`dict["key"]`)을 사용합니다.
아래 첨자가 문자열 상수인 경우 두 옵션은 모두 동일합니다.

> [!NOTE] 
> 식을 아래 첨자로 사용하려면 대괄호 표기법을 사용합니다. 산술 식을 사용하는 경우 대괄호 안의 식을 괄호로 묶어야 합니다.

아래 예제에서 `dict` 및 `arr`은 dynamic 형식의 열입니다.

|식                        | 접근자 식 형식 | 의미                                                                              | 의견                                      |
|----------------------------------|--------------------------|--------------------------------------------------------------------------------------|-----------------------------------------------|
|dict[col]                         | 엔터티 이름(열)     | `col` 열의 값을 키로 사용하여 사전에 대한 아래 첨자를 지정합니다.              | 열은 문자열 형식이어야 합니다.                 | 
|arr[index]                        | 엔터티 인덱스(열)    | `index` 열의 값을 인덱스로 사용하여 배열에 대한 아래 첨자를 지정합니다.              | 열은 정수 또는 부울 형식이어야 합니다.     | 
|arr[-index]                       | 엔터티 인덱스(열)    | 배열 끝에서 'index'번째 값을 검색합니다.                             | 열은 정수 또는 부울 형식이어야 합니다.     |
|arr[(-1)]                         | 엔터티 인덱스             | 배열의 마지막 값을 검색합니다.                                                |                                               |
|arr[toint(indexAsString)]         | 함수 호출            | `indexAsString` 열의 값을 int 형식으로 캐스팅하고, 이를 사용하여 배열에 대한 아래 첨자를 지정합니다. |                                               |
|dict[['where']]                   | 엔터티 이름(열)으로 사용되는 키워드 | `where` 열의 값을 키로 사용하여 사전에 대한 아래 첨자를 지정합니다.    | 일부 쿼리 언어 키워드와 동일한 엔터티 이름을 따옴표로 묶어야 합니다. | 
|dict.['where'] 또는 dict['where']   | 상수                 | `where` 문자열을 키로 사용하여 사전에 대한 아래 첨자를 지정합니다.                              |                                               |

**성능 팁:** 가능하면 상수 아래 첨자를 사용하는 것이 좋습니다.

`dynamic` 값의 하위 개체에 액세스하면 하위 개체의 기본 형식이 다른 경우에도 다른 `dynamic` 값이 생성됩니다. `gettype` 함수를 사용하여 값의 실제 기본 형식을 검색하고, 아래에 나열된 캐스트 함수 중 하나를 사용하여 실제 형식으로 캐스팅합니다.

## <a name="casting-dynamic-objects"></a>동적 개체 캐스팅

> 동적 개체에 대한 아래 첨자가 지정되면 값을 단순 형식으로 캐스팅해야 합니다.

|식 | 값 | 형식|
|---|---|---|
| X | parse_json('[100,101,102]')| array|
|X[0]|parse_json('100')|동적|
|toint(X[1])|101| int|
| Y | parse_json('{"a1":100, "a b c":"2015-01-01"}')| 사전|
|Y.a1|parse_json('100')|동적|
|Y["a b c"]| parse_json("2015-01-01")|동적|
|todate(Y["a b c"])|datetime(2015-01-01)| Datetime|

캐스트 함수는 다음과 같습니다.

* `tolong()`
* `todouble()`
* `todatetime()`
* `totimespan()`
* `tostring()`
* `toguid()`
* `todynamic()`

## <a name="building-dynamic-objects"></a>동적 개체 작성

다음과 같은 몇 가지 함수를 사용하여 새 `dynamic` 개체를 만들 수 있습니다.

* [pack()](../packfunction.md)은 이름/값 쌍에서 속성 모음을 만듭니다.
* [pack_array()](../packarrayfunction.md)는 이름/값 쌍에서 배열을 만듭니다.
* [range()](../rangefunction.md)는 산술 계열의 숫자가 포함된 배열을 만듭니다.
* [zip()](../zipfunction.md)은 두 배열의 "병렬" 값을 단일 배열로 쌍으로 변환합니다.
* [repeat()](../repeatfunction.md)는 반복되는 값이 포함된 배열을 만듭니다.

또한 집계 값이 포함된 `dynamic` 배열을 만드는 다음과 같은 몇 가지 집계 함수가 있습니다.

* [buildschema()](../buildschema-aggfunction.md)는 여러 `dynamic` 값의 집계 스키마를 반환합니다.
* [make_bag()](../make-bag-aggfunction.md)은 그룹 내의 동적 값에 대한 속성 모음을 반환합니다.
* [make_bag_if()](../make-bag-if-aggfunction.md)는 그룹 내의 동적 값에 대한 속성 모음을 반환합니다(조건자 사용).
* [make_list()](../makelist-aggfunction.md)는 모든 값이 순서대로 포함된 배열을 반환합니다.
* [make_list_if()](../makelistif-aggfunction.md)는 모든 값이 순서대로 포함된 배열을 반환합니다(조건자 사용).
* [make_list_with_dvs()](../make-list-with-nulls-aggfunction.md)는 모든 값(null 값 포함)이 순서대로 포함된 배열을 반환합니다.
* [make_set()](../makeset-aggfunction.md)는 모든 고유 값이 포함된 배열을 반환합니다.
* [make_set_iffuel](../makesetif-aggfunction.md)는 모든 고유 값이 포함된 배열을 반환합니다(조건자 사용).

## <a name="operators-and-functions-over-dynamic-types"></a>동적 형식에 대한 연산자 및 함수

스칼라 동적/배열 함수에 대한 전체 목록은 [동적/배열 함수](../scalarfunctions.md#dynamicarray-functions)를 참조하세요.

|연산자 또는 함수|동적 데이터 형식 사용|
|---|---|
| *value* `in` *array*| == *value* 인 *array* 의 요소가 있으면 True<br/>`where City in ('London', 'Paris', 'Rome')`
| *value* `!in` *array*| == *value* 인 *array* 의 요소가 없으면 True
|[`array_length(`array`)`](../arraylengthfunction.md)| 배열이 아니면 Null
|[`bag_keys(`bag`)`](../bagkeysfunction.md)| 동적 속성 모음 개체의 모든 루트 키를 열거합니다.
|[`bag_merge(`bag1,...,bagN`)`](../bag-merge-function.md)| 동적 속성 모음을 모든 속성이 병합된 동적 속성 모음으로 병합합니다.
|[`extractjson(`path,object`)`](../extractjsonfunction.md)|path를 사용하여 object를 탐색합니다.
|[`parse_json(`source`)`](../parsejsonfunction.md)| JSON 개체를 동적 개체로 변환합니다.
|[`range(`from,to,step`)`](../rangefunction.md)| 값의 배열
|[`mv-expand` listColumn](../mvexpandoperator.md) | 각 값에 대한 행을 지정된 셀의 목록에 복제합니다.
|[`summarize buildschema(`열`)`](../buildschema-aggfunction.md) |열 내용에서 형식 스키마를 유추
|[`summarize make_bag(`열`)`](../make-bag-aggfunction.md) | 열의 속성 모음(사전) 값을 하나의 속성 모음으로 병합합니다(키 중복 포함 안 함).
|[`summarize make_bag_if(`열,조건자`)`](../make-bag-if-aggfunction.md) | 열의 속성 모음(사전) 값을 하나의 속성 모음으로 병합합니다(키 중복 포함 안 함, 조건자 사용).
|[`summarize make_list(`열`)` ](../makelist-aggfunction.md)| 행 그룹을 평면화하고 열의 값을 배열에 넣습니다.
|[`summarize make_list_if(`열,조건자`)` ](../makelistif-aggfunction.md)| 행 그룹을 평면화하고, 열 값을 배열에 배치합니다(조건자 사용).
|[`summarize make_list_with_nulls(`열`)` ](../make-list-with-nulls-aggfunction.md)| 행 그룹을 평면화하고, 열 값(null 값 포함)을 배열에 배치합니다.
|[`summarize make_set(`열`)`](../makeset-aggfunction.md) | 행 그룹을 평면화하고 열의 값을 중복 없이 배열에 넣습니다.
