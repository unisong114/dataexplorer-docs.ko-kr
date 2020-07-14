---
title: 동적 데이터 형식-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 동적 데이터 형식에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 07/09/2020
ms.openlocfilehash: e909754a040308d752b19155e1e69a10097ab219
ms.sourcegitcommit: 2126c5176df272d149896ac5ef7a7136f12dc3f3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/13/2020
ms.locfileid: "86280510"
---
# <a name="the-dynamic-data-type"></a>동적 데이터 형식

`dynamic`스칼라 데이터 형식은 아래 목록에서 다른 스칼라 데이터 형식의 값과 배열 및 속성 모음을 모두 사용할 수 있다는 특수 합니다. 특히 값은 `dynamic` 다음과 같을 수 있습니다.

* N.
* 기본 스칼라 데이터 형식인,,,,,, `bool` `datetime` `guid` `int` `long` `real` `string` 및 `timespan` 값입니다.
* 0부터 시작 `dynamic` 하는 인덱스가 포함 된 0 개 이상의 값을 보유 하는 값의 배열입니다.
* 고유 `string` 값을 값에 매핑하는 속성 모음입니다 `dynamic` .
  속성 모음에는 고유 값으로 인덱싱된 매핑 ("슬롯" 이라고 함)이 0 개 이상 있습니다 `string` . 슬롯은 순서가 지정 되지 않습니다.

> [!NOTE]
> * 형식의 값 `dynamic` 은 1mb (2 ^ 20)로 제한 됩니다.
> * 형식이 json `dynamic` 처럼 표시 되지만 json 모델에는 없는 값 (예:,,, `long` `real` `datetime` `timespan` , 및 `guid` )이 포함 될 수 있습니다.
>   따라서 `dynamic` 값을 json 표현으로 serialize 할 때 json을 표현할 수 없는 값은 값으로 serialize 됩니다 `string` . 반대로, Kusto는 구문 분석 될 수 있는 경우 문자열을 강력한 형식의 값으로 구문 분석 합니다.
>   이는 `datetime` ,, `real` `long` 및 `guid` 형식에 적용 됩니다. 
>   JSON 개체 모델에 대 한 자세한 내용은 [json.org](https://json.org/)를 참조 하세요.
> * Kusto는 속성 모음에서 이름-값 매핑의 순서를 유지 하려고 시도 하지 않으므로 순서를 유지할 수 없습니다. 예를 들어, 값으로 표현 될 때 서로 다른 결과를 얻을 수 있도록 동일한 매핑 집합을 가진 두 개의 속성 모음이 가능 `string` 합니다.

## <a name="dynamic-literals"></a>동적 리터럴

형식의 리터럴은 다음과 같습니다 `dynamic` .

`dynamic(` *값* `)`

*값* 은 다음과 같을 수 있습니다.

* `null`이 경우 리터럴은 null 동적 값인를 `dynamic(null)` 나타냅니다.
* 다른 스칼라 데이터 형식 리터럴 .이 경우 리터럴은 `dynamic` "내부" 형식의 리터럴을 나타냅니다. 예를 들어 `dynamic(4)` 는 long 스칼라 데이터 형식의 값 4를 포함 하는 동적 값입니다.
* 동적 또는 다른 리터럴의 배열입니다. `[` *listofvalues* `]` . 예를 들어 `dynamic([1, 2, "hello"])` 는 세 개의 요소, 두 `long` 값 및 하나의 값의 동적 배열입니다 `string` .
* 속성 모음: `{` *이름* `=` *값* `}` .... 예를 들어 `dynamic({"a":1, "b":{"a":2}})` 는 두 개의 슬롯, 및가 있는 속성 모음으로, `a` `b` 두 번째 슬롯은 또 다른 속성 모음입니다.

```kusto
print o=dynamic({"a":123, "b":"hello", "c":[1,2,3], "d":{}})
| extend a=o.a, b=o.b, c=o.c, d=o.d
```

편의상,,,,, `dynamic` `datetime` `timespan` `real` `long` `guid` `bool` 및 `dynamic` 형식과 함께 쿼리 텍스트 자체에 나타나는 리터럴에 다른 kusto 리터럴을 포함할 수도 있습니다.
JSON을 통한이 확장은 문자열 (예: 함수를 사용 하는 경우 `parse_json` 또는 수집 데이터)을 구문 분석할 때 사용할 수 없지만 다음 작업을 수행할 수 있습니다.

```kusto
print d=dynamic({"a": datetime(1970-05-11)})
```

`string`JSON 인코딩 규칙을 따르는 값을 값으로 구문 분석 하려면 `dynamic` 함수를 사용 `parse_json` 합니다. 예를 들어:

* `parse_json('[43, 21, 65]')` - 숫자의 배열
* `parse_json('{"name":"Alan", "age":21, "address":{"street":432,"postcode":"JLK32P"}}')`-사전
* `parse_json('21')` - 숫자를 포함하는 동적 형식의 단일 값
* `parse_json('"21"')` - 문자열을 포함하는 동적 형식의 단일 값
* `parse_json('{"a":123, "b":"hello", "c":[1,2,3], "d":{}}')`-위의 예제와 같은 값을 제공 합니다 `o` .

> [!NOTE]
> JavaScript와 달리 JSON은 `"` 문자열과 속성 모음 속성 이름 앞뒤에 큰따옴표 () 문자를 사용 합니다.
> 따라서 일반적으로 작은따옴표 () 문자를 사용 하 여 JSON으로 인코딩된 문자열 리터럴을 더 쉽게 따옴표로 묶을 수 `'` 있습니다.
  
다음 예에서는 열과 열을 포함 하는 테이블을 정의한 `dynamic` 다음 단일 레코드로 수집 하는 방법을 보여 줍니다 `datetime` . 또한 CSV 파일에서 JSON 문자열을 인코딩하는 방법도 보여 줍니다.

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

|Timestamp                   | 추적                                                 |
|----------------------------|-------------------------------------------------------|
|2015-01-01 00:00:00.0000000 | {"EventType": "Demo", "EventValue": "큰따옴표 선호!"}|

## <a name="dynamic-object-accessors"></a>동적 개체 접근자

사전 첨자를 표시 하려면 점 표기법 ( `dict.key` ) 또는 대괄호 표기법 () 중 하나를 사용 `dict["key"]` 합니다.
첨자가 문자열 상수인 경우 두 옵션은 모두 동일 합니다.

> [!NOTE] 
> 식을 첨자로 사용 하려면 대괄호 표기법을 사용 합니다. 산술 식을 사용할 때는 괄호 안의 식을 괄호로 묶어야 합니다.

아래 예제에서 `dict` 및 `arr` 는 동적 형식의 열입니다.

|식                        | 접근자 식 형식 | 의미                                                                              | 의견                                      |
|----------------------------------|--------------------------|--------------------------------------------------------------------------------------|-----------------------------------------------|
|dict [col]                         | 엔터티 이름 (열)     | 열의 값을 키로 사용 하 여 사전 첨자를 사용 합니다. `col`              | 열은 문자열 형식 이어야 합니다.                 | 
|arr [인덱스]                        | 엔터티 인덱스 (열)    | 열의 값을 인덱스로 사용 하 여 배열의 아래 첨자 `index`              | 열은 정수 또는 부울 형식 이어야 합니다.     | 
|arr [-index]                       | 엔터티 인덱스 (열)    | 배열의 끝에서 ' index' 번째 값을 검색 합니다.                             | 열은 정수 또는 부울 형식 이어야 합니다.     |
|arr [(-1)]                         | 엔터티 인덱스             | 배열의 마지막 값을 검색 합니다.                                                |                                               |
|arr [toint (indexAsString)]         | 함수 호출            | 열의 값을 int로 캐스팅 하 고이를 사용 하 여 `indexAsString` 배열의 첨자를 사용 합니다. |                                               |
|dict [[' where ']]                   | 엔터티 이름 (열)으로 사용 되는 키워드 | 열의 값을 키로 사용 하 여 사전 첨자를 사용 합니다. `where`    | 일부 쿼리 언어 키워드와 동일한 엔터티 이름은 따옴표로 묶어야 합니다. | 
|dict. [' where '] 또는 dict [' where ']   | 상수                 | 문자열을 키로 사용 하 여 사전 첨자 `where`                              |                                               |

**성능 팁:** 가능 하면 상수 첨자를 사용 하는 것이 좋습니다.

`dynamic` `dynamic` 하위 개체의 내부 형식이 다른 경우에도 값의 하위 개체에 액세스 하면 다른 값이 생성 됩니다. 함수를 사용 하 여 `gettype` 값의 실제 내부 형식과 아래에 나열 된 cast 함수를 검색 하 여 실제 형식으로 캐스팅 합니다.

## <a name="casting-dynamic-objects"></a>동적 개체 캐스팅

> 동적 개체를 첨자 한 후에는 값을 단순 형식으로 캐스팅 해야 합니다.

|식 | 값 | 형식|
|---|---|---|
| X | parse_json (' [100101102] ')| array|
|X [0]|parse_json (' 100 ')|동적|
|toint (X [1])|101| int|
| Y | parse_json (' {"a1": 100, "a b c": "2015-01-01"} ')| 사전|
|X-y|parse_json (' 100 ')|동적|
|Y ["a b c"]| parse_json ("2015-01-01")|동적|
|todate (Y ["a b c"])|datetime (2015-01-01)| Datetime|

Cast 함수는 다음과 같습니다.

* `tolong()`
* `todouble()`
* `todatetime()`
* `totimespan()`
* `tostring()`
* `toguid()`
* `todynamic()`

## <a name="building-dynamic-objects"></a>동적 개체 빌드

여러 가지 함수를 사용 하 여 새 개체를 만들 수 있습니다 `dynamic` .

* [pack ()](../packfunction.md) 은 이름/값 쌍 으로부터 속성 모음을 만듭니다.
* [pack_array ()](../packarrayfunction.md) 는 이름/값 쌍에서 배열을 만듭니다.
* [range ()](../rangefunction.md) 는 산술 일련의 숫자를 사용 하 여 배열을 만듭니다.
* [zip ()](../zipfunction.md) 은 두 배열의 "parallel" 값을 단일 배열로 나눕니다.
* [repeat ()](../repeatfunction.md) 는 반복 되는 값을 사용 하 여 배열을 만듭니다.

또한 `dynamic` 집계 된 값을 포함 하는 배열을 만드는 여러 집계 함수가 있습니다.

* [buildschema ()](../buildschema-aggfunction.md) 는 여러 값의 집계 스키마를 반환 합니다 `dynamic` .
* [make_bag ()](../make-bag-aggfunction.md) 는 그룹 내의 동적 값에 대 한 속성 모음을 반환 합니다.
* [make_bag_if ()](../make-bag-if-aggfunction.md) 는 그룹 내에서 조건자를 사용 하 여 동적 값의 속성 모음을 반환 합니다.
* [make_list ()](../makelist-aggfunction.md) 는 모든 값을 순서 대로 포함 하는 배열을 반환 합니다.
* [make_list_if ()](../makelistif-aggfunction.md) 는 모든 값을 포함 하는 배열을 순서 대로 (조건자 포함) 반환 합니다.
* [make_list_with_nulls ()](../make-list-with-nulls-aggfunction.md) 는 null 값을 포함 하 여 모든 값을 순서 대로 포함 하는 배열을 반환 합니다.
* [make_set ()](../makeset-aggfunction.md) 은 모든 고유 값을 포함 하는 배열을 반환 합니다.
* [make_set_if ()](../makesetif-aggfunction.md) 는 조건자를 사용 하 여 모든 고유 값을 포함 하는 배열을 반환 합니다.

## <a name="operators-and-functions-over-dynamic-types"></a>동적 형식에 대한 연산자 및 함수

|연산자 또는 함수|동적 데이터 형식 사용|
|---|---|
| *value* `in` *array*| == *value*인 *array*의 요소가 있으면 True<br/>`where City in ('London', 'Paris', 'Rome')`
| *value* `!in` *array*| == *value*인 *array*의 요소가 없으면 True
|[`array_length(`배열과`)`](../arraylengthfunction.md)| 배열이 아니면 Null
|[`bag_keys(`백`)`](../bagkeysfunction.md)| 동적 속성 모음 개체의 모든 루트 키를 열거 합니다.
|[`bag_merge(`bag1,..., bagN`)`](../bag-merge-function.md)| 모든 속성을 병합 하 여 동적 속성 모음에 동적 속성 모음을 병합 합니다.
|[`extractjson(`경로, 개체`)`](../extractjsonfunction.md)|path를 사용하여 object를 탐색합니다.
|[`parse_json(`원본`)`](../parsejsonfunction.md)| JSON 개체를 동적 개체로 변환합니다.
|[`range(`부터,까지 단계`)`](../rangefunction.md)| 값의 배열
|[`mv-expand`listColumn](../mvexpandoperator.md) | 각 값에 대한 행을 지정된 셀의 목록에 복제합니다.
|[`summarize buildschema(`열의`)`](../buildschema-aggfunction.md) |열 내용에서 형식 스키마를 유추
|[`summarize make_bag(`열의`)`](../make-bag-aggfunction.md) | 키 중복 없이 열의 속성 모음 (사전) 값을 하나의 속성 모음으로 병합 합니다.
|[`summarize make_bag_if(`열, 조건자`)`](../make-bag-if-aggfunction.md) | 키 중복 (조건자 사용) 없이 열의 속성 모음 (사전) 값을 하나의 속성 모음으로 병합 합니다.
|[`summarize make_list(`열 `)`](../makelist-aggfunction.md)| 행 그룹을 평면화하고 열의 값을 배열에 넣습니다.
|[`summarize make_list_if(`열, 조건자 `)`](../makelistif-aggfunction.md)| 행 그룹을 평면화 하 고 열의 값을 배열에 배치 합니다 (조건자 포함).
|[`summarize make_list_with_nulls(`열 `)`](../make-list-with-nulls-aggfunction.md)| 행 그룹을 평면화 하 고 열의 값을 null 값을 포함 하 여 배열에 넣습니다.
|[`summarize make_set(`열의`)`](../makeset-aggfunction.md) | 행 그룹을 평면화하고 열의 값을 중복 없이 배열에 넣습니다.
