---
title: 동적 데이터 형식 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 동적 데이터 형식에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/12/2020
ms.openlocfilehash: e1cdb6e5af20b326198a7447c50c24e5f632d237
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81509880"
---
# <a name="the-dynamic-data-type"></a>동적 데이터 형식

스칼라 `dynamic` 데이터 유형은 아래 목록에서 다른 스칼라 데이터 형식의 모든 값과 배열 및 속성 백을 사용할 수 있다는 점에서 특별합니다. 특히 값은 `dynamic` 다음과 같은 수 있습니다.

* Null.
* 기본 스칼라 데이터 형식의 값: . `bool` `datetime` `guid` `int` `long` `real` `string` `timespan`
* 0기반 `dynamic` 인덱싱을 통해 0개 이상의 값을 보유하는 값 의 배열입니다.
* 고유한 `string` 값을 값에 매핑하는 `dynamic` 속성 백입니다.
  속성 가방에는 고유한 `string` 값으로 인덱싱된 매핑("슬롯"이라고 함)이 0개 이상 있습니다. 슬롯의 순서가 지정되지 않았습니다.

> [!NOTE]
> 형식 `dynamic` 값은 1MB(2^20)로 제한됩니다.

> [!NOTE]
> 형식이 `dynamic` JSON과 유사으로 나타나지만 JSON `long`모델이 JSON(예: , , " `real` `datetime` `timespan`및)에 `guid`존재하지 않기 때문에 JSON 모델이 나타내지 않는 값을 보유할 수 있습니다.
> 따라서 JSON `dynamic` 표현으로 값을 직렬화할 때 JSON이 나타낼 수 `string` 없는 값은 값으로 직렬화됩니다. 반대로 Kusto는 문자열을 강력한 형식의 값으로 구문 분석할 수 있는 경우 구문 분석합니다.
> 이 에 `datetime` `real`대 `long`한 `guid` 적용 됩니다 . JSON 개체 모델에 대한 자세한 내용은 [json.org](https://json.org/)참조를 참조하십시오.

> [!NOTE]
> Kusto는 속성 가방에서 이름 대 값 매핑의 순서를 유지하려고 시도하지 않으므로 보존할 순서를 가정할 수 없습니다. 예를 들어, 동일한 매핑 집합을 가진 두 속성 백이 값으로 `string` 표현될 때 다른 결과를 얻을 수 있습니다.

## <a name="dynamic-literals"></a>동적 리터럴

문자 그대로 형식은 `dynamic` 다음과 같습니다.

`dynamic(` *값* `)`

*값은* 다음과 같은 것입니다.

* `null`이 경우 리터럴은 null 동적 값을 `dynamic(null)`나타냅니다.
* 또 다른 스칼라 데이터 형식 리터럴(이 경우 `dynamic` 리터럴은 "내부" 형식)의 리터럴을 나타냅니다. 예를 들어 `dynamic(4)` 긴 스칼라 데이터 형식의 값 4를 보유하는 동적 값입니다.
* 동적 또는 기타 리터럴의 `[` 배열: *ListOfValues* `]`. 예를 들어 `dynamic([1, 2, "hello"])` 세 가지 요소, 두 `long` 개의 `string` 값과 하나의 값으로 구성된 동적 배열입니다.
* 속성 `{` *가방:이름* `=` *값...* `}`. 예를 들어 `dynamic({"a":1, "b":{"a":2}})` 두 개의 슬롯이 `a`있는 속성 `b`백과 두 번째 슬롯이 다른 속성 가방인 속성 입니다.

```kusto
print o=dynamic({"a":123, "b":"hello", "c":[1,2,3], "d":{}})
| extend a=o.a, b=o.b, c=o.c, d=o.d
```

편의를 `dynamic` 위해 쿼리 텍스트 자체에 나타나는 리터럴에는 다른 Kusto 리터럴(예: `datetime` 리터럴, `timespan` 리터럴 등)도 포함될 수 있습니다. JSON에 대한 이 확장은 문자열을 구문 분석할 `parse_json` 때(예: 함수 사용 또는 데이터 수집 시) 사용할 수 없지만 이 작업을 수행할 수 있습니다.

```kusto
print d=dynamic({"a": datetime(1970-05-11)})
```

JSON 인코딩 `string` 규칙을 따르는 값을 `dynamic` 값으로 구문 분석하려면 `parse_json` 함수를 사용합니다. 다음은 그 예입니다.

* `parse_json('[43, 21, 65]')` - 숫자의 배열
* `parse_json('{"name":"Alan", "age":21, "address":{"street":432,"postcode":"JLK32P"}}')`- 사전
* `parse_json('21')` - 숫자를 포함하는 동적 형식의 단일 값
* `parse_json('"21"')` - 문자열을 포함하는 동적 형식의 단일 값
* `parse_json('{"a":123, "b":"hello", "c":[1,2,3], "d":{}}')`- 위의 예와 `o` 동일한 값을 제공합니다.

> [!NOTE]
> JavaScript와 달리 JSON은 문자열 및 속성`"`가방 속성 이름 주위에 double quote () 문자를 사용하도록 명령합니다.
> 따라서 일반적으로 단일 따옴표 ()`'`문자를 사용 하 여 JSON 인코딩 된 문자열 리터럴을 인용 하는 것이 더 쉽습니다.
  
다음 예제에서는 `dynamic` 열(열뿐만 아니라)을 `datetime` 포함하는 테이블을 정의한 다음 단일 레코드로 인더스트하는 방법을 보여 주며 있습니다. 또한 CSV 파일에서 JSON 문자열을 인코딩하는 방법을 보여 줍니다.

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
|2015-01-01 00:00:00.0000000 | {"EventType":"데모","이벤트밸류","두 번 따옴표 사랑!"}|

## <a name="dynamic-object-accessors"></a>동적 개체 접근자

사전을 서브스크립팅하려면 점`dict.key`표기() 또는 대괄호`dict["key"]`표기()를 사용합니다.
하위 스크립트가 문자열 상수인 경우 두 옵션 모두 동일합니다.

> [!NOTE] 
> 식을 하위 스크립트로 사용하려면 괄호 표기와 함께 사용합니다. 산술 식을 사용하는 경우 괄호 안의 식은 괄호로 줄 바꿈되어야 합니다.

아래 `dict` 예제에서 동적 `arr` 형식의 열입니다.

|식                        | 접근자 식 형식 | 의미                                                                              | 주석                                      |
|----------------------------------|--------------------------|--------------------------------------------------------------------------------------|-----------------------------------------------|
|[콜]                         | 엔터티 이름(열)     | 열값을 `col` 키로 사용하여 사전을 하위 스크립트로 지정합니다.              | 열은 형식 문자열이어야 합니다.                 | 
|arr[인덱스]                        | 엔터티 인덱스(열)    | 열값을 `index` 인덱스로 사용하여 배열을 하위 스크립트로 만듭니다.              | 열은 형식 정수 또는 부울이어야 합니다.     | 
|arr[-인덱스]                       | 엔터티 인덱스(열)    | 배열의 끝에서 'index'-th 값을 검색합니다.                             | 열은 형식 정수 또는 부울이어야 합니다.     |
|arr[(-1)]                         | 엔터티 인덱스             | 배열의 마지막 값을 검색합니다.                                                |                                               |
|arr[toint(인덱스AsString)]         | 함수 호출            | 열 `indexAsString` 값을 int에 캐스팅하고 이를 사용하여 배열을 비문 |                                               |
|['어디에]]                   | 엔터티 이름으로 사용되는 키워드(열) | 열 `where` 값을 키로 사용하여 사전을 하위 스크립트로 지정합니다.    | 일부 쿼리 언어 키워드와 동일한 엔터티 이름을 따옴표로 지정해야 합니다. | 
|dict.['where'] 또는 dict['where']   | 상수                 | 문자열을 키로 `where` 사용하는 사전을 비문                              |                                               |

**성능 팁:** 가능하면 상수 하위 스크립트를 사용하는 것을 선호합니다.

`dynamic` 값의 하위 개체에 액세스하면 `dynamic` 하위 개체에 다른 기본 형식이 있더라도 다른 값이 생성됩니다. 함수를 `gettype` 사용하여 값의 실제 기본 형식과 아래에 나열된 캐스트 함수를 검색하여 실제 유형으로 캐스팅합니다.

## <a name="casting-dynamic-objects"></a>동적 오브젝트 캐스팅

> 동적 개체를 스크립팅한 후 값을 단순 유형으로 캐스팅해야 합니다.

|식 | 값 | Type|
|---|---|---|
| X | parse_json('100,101,102]')| array|
|X[0]|parse_json('100')|동적|
|토인트(X[1])|101| int|
| Y | parse_json('{"a1":100, "b c":"2015-01-01"}))| 사전|
|Y.a1|parse_json('100')|동적|
|Y["b c"]| parse_json("2015-01-01")|동적|
|날짜(Y["b c"])|날짜 시간(2015-01-01)| Datetime|

캐스트 함수는 다음과 같습니다.

* `tolong()`
* `todouble()`
* `todatetime()`
* `totimespan()`
* `tostring()`
* `toguid()`
* `todynamic()`

## <a name="building-dynamic-objects"></a>동적 객체 작성

여러 기능을 통해 새 `dynamic` 개체를 만들 수 있습니다.

* [pack()은](../packfunction.md) 이름/값 쌍에서 속성 백을 만듭니다.
* [pack_array()는](../packarrayfunction.md) 이름/값 쌍에서 배열을 만듭니다.
* [range()는](../rangefunction.md) 산술 계열의 숫자가 있는 배열을 만듭니다.
* [zip()은](../zipfunction.md) 두 배열의 "병렬" 값을 단일 배열로 페어링합니다.
* [repeat()는](../repeatfunction.md) 반복된 값으로 배열을 만듭니다.

또한 집계된 값을 보유하는 `dynamic` 배열을 만드는 여러 집계 함수가 있습니다.

* [buildschema()는](../buildschema-aggfunction.md) 여러 `dynamic` 값의 집계 스키마를 반환합니다.
* [make_bag()](../make-bag-aggfunction.md) 그룹 내에서 동적 값의 속성 백을 반환합니다.
* [make_bag_if()](../make-bag-if-aggfunction.md) 그룹 내의 동적 값의 속성 백을 반환합니다(조건자).
* [make_list())는](../makelist-aggfunction.md) 모든 값을 순서대로 보유하는 배열을 반환합니다.
* [make_list_if()는](../makelistif-aggfunction.md) 조건자()를 사용하여 모든 값을 순서대로 보유하는 배열을 반환합니다.
* [make_list_with_nulls()](../make-list-with-nulls-aggfunction.md) null 값을 포함하여 모든 값을 순서대로 보유하는 배열을 반환합니다.
* [make_set()](../makeset-aggfunction.md) 모든 고유 값을 보유 하는 배열을 반환 합니다.
* [make_set_if()](../makesetif-aggfunction.md) 모든 고유 값을 보유 하는 배열을 반환 합니다 (조건자).

## <a name="operators-and-functions-over-dynamic-types"></a>동적 형식에 대한 연산자 및 함수

|||
|---|---|
| *value* `in` *array*| == *value*인 *array*의 요소가 있으면 True<br/>`where City in ('London', 'Paris', 'Rome')`
| *value* `!in` *array*| == *value*인 *array*의 요소가 없으면 True
|[`array_length(`배열`)`](../arraylengthfunction.md)| 배열이 아니면 Null
|[`bag_keys(`가방`)`](../bagkeysfunction.md)| 동적 속성-bag 개체의 모든 루트 키를 배분합니다.
|[`extractjson(`경로, 개체`)`](../extractjsonfunction.md)|path를 사용하여 object를 탐색합니다.
|[`parse_json(`소스`)`](../parsejsonfunction.md)| JSON 개체를 동적 개체로 변환합니다.
|[`range(`에서,로, 단계`)`](../rangefunction.md)| 값의 배열
|[`mv-expand`목록열](../mvexpandoperator.md) | 각 값에 대한 행을 지정된 셀의 목록에 복제합니다.
|[`summarize buildschema(`열`)`](../buildschema-aggfunction.md) |열 내용에서 형식 스키마를 유추
|[`summarize make_bag(`열`)`](../make-bag-aggfunction.md) | 키 중복 없이 열의 속성 백(사전) 값을 하나의 속성 백으로 병합합니다.
|[`summarize make_bag_if(`열, 술어`)`](../make-bag-if-aggfunction.md) | 키 중복 없이 열의 속성 백(사전) 값을 하나의 속성 백으로 병합합니다(조건자 사용).
|[`summarize make_list(`열`)`](../makelist-aggfunction.md)| 행 그룹을 평면화하고 열의 값을 배열에 넣습니다.
|[`summarize make_list_if(`열, 술어`)`](../makelistif-aggfunction.md)| 행 그룹을 평평하게 하고 배열에 열 값을 넣습니다(조건자).
|[`summarize make_list_with_nulls(`열`)`](../make-list-with-nulls-aggfunction.md)| 행 그룹을 평평하게 하고 null 값을 포함하여 배열에 열값을 넣습니다.
|[`summarize make_set(`열`)`](../makeset-aggfunction.md) | 행 그룹을 평면화하고 열의 값을 중복 없이 배열에 넣습니다.
|[`summarize make_bag(`열`)`](../make-bag-aggfunction.md) | 키 중복 없이 열의 속성 백(사전) 값을 하나의 속성 백으로 병합합니다.