---
title: mv-expand 연산자 - Azure Data Explorer
description: 이 문서에서는 Azure Data Explorer의 mv-expand 연산자에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2019
ms.localizationpriority: high
ms.openlocfilehash: 87f8bb5faecea32c24ab78b6844d3bba18a540bd
ms.sourcegitcommit: 7a94cdf8c07b15e594dd8abc10091afe5f716609
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/25/2021
ms.locfileid: "105543105"
---
# <a name="mv-expand-operator"></a>mv-expand 연산자

다중 값 동적 배열 또는 속성 모음을 여러 레코드로 확장합니다.

`mv-expand`는 `summarize` ... `make-list()` 및 `make-series`와 같이 여러 값을 단일 [동적](./scalar-data-types/dynamic.md) 유형의 배열 또는 속성 모음으로 압축하는 집계 연산자의 반대라고 설명할 수 있습니다.
(스칼라) 배열 또는 속성 모음의 각 요소는 연산자의 출력에 새 레코드를 생성합니다. 확장되지 않은 입력의 모든 열은 출력의 모든 레코드에 복제됩니다.

## <a name="syntax"></a>Syntax

*T* `| mv-expand ` [`bagexpansion=`(`bag` | `array`)] [`with_itemindex=`*IndexColumnName*] *ColumnName* [`to typeof(` *Typename*`)`] [`,` *ColumnName* ...] [`limit` *Rowlimit*]

*T* `| mv-expand ` [`bagexpansion=`(`bag` | `array`)] *Name* `=` *ArrayExpression* [`to typeof(`*Typename*`)`] [, [*Name* `=`] *ArrayExpression* [`to typeof(`*Typename*`)`] ...] [`limit` *Rowlimit*]

## <a name="arguments"></a>인수

* *ColumnName*, *ArrayExpression*: 배열 또는 속성 모음을 포함하는 `dynamic` 유형의 값을 가진 열 참조 또는 스칼라 식입니다. 배열 또는 속성 모음의 개별 최상위 요소는 여러 레코드로 확장됩니다.<br>
  *ArrayExpression* 이 사용되고 *Name* 이 입력 열 이름과 같지 않으면 확장된 값이 출력의 새 열로 확장됩니다.
  그렇지 않으면 기존 *ColumnName* 이 대체됩니다.

* *Name:* 새 열에 대한 이름입니다.

* *Typename:* `mv-expand` 연산자에 의해 생성되는 열의 형식이 되는 배열 요소의 기본 형식을 나타냅니다. 형식 적용 작업은 캐스트 전용이며, 구문 분석 또는 형식 변환을 포함하지 않습니다. 선언된 형식을 따르지 않는 배열 요소는 `null` 값이 됩니다.

* *RowLimit:* 각각의 원래 행에서 생성되는 최대 행 수입니다. 기본값은 2147483647입니다. 

  > [!NOTE]
  > `mvexpand`는 `mv-expand` 연산자의 레거시 형식 및 사용되지 않는 형식입니다. 레거시 버전의 기본 행 제한은 128입니다.

* *IndexColumnName:* `with_itemindex`가 지정된 경우 출력에는 원래 확장된 컬렉션에 있는 항목의 인덱스(0부터 시작)를 포함하는 다른 열(*IndexColumnName*)이 포함됩니다. 

## <a name="returns"></a>반환

입력의 각 레코드에 대해 연산자는 다음과 같은 방식으로 결정된 대로 0개, 1개 또는 많은 레코드를 출력에 반환합니다.

1. 확장되지 않은 입력 열이 원래 값과 함께 출력에 나타납니다.
   단일 입력 레코드를 여러 출력 레코드로 확장하면 값은 모든 레코드에 복제됩니다.

1. 확장된 각 *ColumnName* 또는 *ArrayExpression* 에 대해 [아래](#modes-of-expansion)에 설명된 대로 각 값에 대해 출력 레코드 수가 결정됩니다. 각 입력 레코드에 대해 최대 출력 레코드 수가 계산됩니다. 모든 배열 또는 속성 모음이 "병렬"로 확장되어 누락된 값(있는 경우)은 null 값으로 대체됩니다. 요소는 원래 배열/모음에 표시되는 순서대로 행으로 확장됩니다.

1. 동적 값이 null이면 해당 값(null)에 대해 단일 레코드가 생성됩니다.
   동적 값이 빈 배열 또는 속성 모음인 경우 해당 값에 대한 레코드가 생성되지 않습니다.
   그렇지 않으면, 동적 값에 요소가 있으므로 많은 레코드가 생성됩니다.

`to typeof()` 절을 사용하여 명시적으로 입력하지 않는 한 확장 열은 `dynamic` 유형입니다.

### <a name="modes-of-expansion"></a>확장 모드

속성 모음 확장의 두 가지 모드가 지원됩니다.

* `bagexpansion=bag` 또는 `kind=bag`: 속성 모음이 단일 항목 속성 모음으로 확장됩니다. 이 모드가 기본 모드입니다.
* `bagexpansion=array` 또는 `kind=array`: 속성 모음이 두 요소로 이루어진 `[`*key*`,`*value*`]` 배열 구조로 확장되며 키 및 값에 대한 균일한 액세스가 가능합니다. 이 모드에서는 예를 들어 속성 이름에 대해 고유한 카운트 집계를 실행할 수도 있습니다. 

## <a name="examples"></a>예제

### <a name="single-column"></a>단일 열

단일 열의 간단한 확장:

<!-- csl: https://help.kusto.windows.net:443/Samples -->
 ```kusto
datatable (a:int, b:dynamic)[1,dynamic({"prop1":"a", "prop2":"b"})]
| mv-expand b 
```

|a|b|
|---|---|
|1|{"prop1":"a"}|
|1|{"prop2":"b"}|

### <a name="zipped-two-columns"></a>두 개의 열 압축

두 열을 확장하면 먼저 해당 열을 '압축'한 다음, 확장합니다.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
datatable (a:int, b:dynamic, c:dynamic)[1,dynamic({"prop1":"a", "prop2":"b"}), dynamic([5, 4, 3])]
| mv-expand b, c
```

|a|b|c|
|---|---|---|
|1|{"prop1":"a"}|5|
|1|{"prop2":"b"}|4|
|1||3|

### <a name="cartesian-product-of-two-columns"></a>두 열의 데카르트 곱

두 열을 확장하는 카티전 곱을 가져오려면 다음 열을 차례로 확장합니다.

<!-- csl: https://kuskusdfv3.kusto.windows.net/Kuskus -->
```kusto
datatable (a:int, b:dynamic, c:dynamic)
  [
  1,
  dynamic({"prop1":"a", "prop2":"b"}),
  dynamic([5, 6])
  ]
| mv-expand b
| mv-expand c
```

|a|b|c|
|---|---|---|
|1|{  "prop1": "a"}|5|
|1|{  "prop1": "a"}|6|
|1|{  "prop2": "b"}|5|
|1|{  "prop2": "b"}|6|

### <a name="convert-output"></a>출력 변환

mv-expand의 출력을 특정 형식(기본값: 동적)으로 강제하려면 `to typeof`를 사용합니다.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
datatable (a:string, b:dynamic, c:dynamic)["Constant", dynamic([1,2,3,4]), dynamic([6,7,8,9])]
| mv-expand b, c to typeof(int)
| getschema 
```

ColumnName|ColumnOrdinal|DateType|ColumnType
-|-|-|-
a|0|System.String|문자열
b|1|System.Object|동적
c|2|System.Int32|int

`b` 열은 `dynamic`으로 반환되고 `c` 열은 `int`로 반환됩니다.

### <a name="using-with_itemindex"></a>With_itemindex 사용

`with_itemindex`를 사용하여 배열 확장:

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
range x from 1 to 4 step 1
| summarize x = make_list(x)
| mv-expand with_itemindex=Index x
```

|x|인덱스|
|---|---|
|1|0|
|2|1|
|3|2|
|4|3|

## <a name="see-also"></a>참고 항목

* 추가 예제는 [시간 경과에 따른 라이브 활동의 차트 수](./samples.md#chart-concurrent-sessions-over-time)를 참조하세요.
* [mv-apply](./mv-applyoperator.md) 연산자.
* mv-expand의 반대 함수인 [make_list()를 요약](makelist-aggfunction.md)합니다.
* 속성 모음 키를 사용하여 동적 JSON 개체를 열로 확장하는 [bag_unpack()](bag-unpackplugin.md) 플러그 인입니다.
