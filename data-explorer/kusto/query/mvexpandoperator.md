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
ms.openlocfilehash: f14ec4fa24765053711d60f7d2365755b45adbab
ms.sourcegitcommit: c6cb2b1071048daa872e2fe5a1ac7024762c180e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/07/2020
ms.locfileid: "96774642"
---
# <a name="mv-expand-operator"></a>mv-expand 연산자

다중 값 배열 또는 속성 모음을 확장합니다.

`mv-expand`는 컬렉션의 각 값이 별도의 행을 갖도록 [동적](./scalar-data-types/dynamic.md) 형식의 배열 또는 속성 모음 열에 적용됩니다. 확장된 행의 다른 열은 모두 중복됩니다. 

## <a name="syntax"></a>Syntax

*T* `| mv-expand ` [`bagexpansion=`(`bag` | `array`)] [`with_itemindex=`*IndexColumnName*] *ColumnName* [`,` *ColumnName* ...] [`limit` *Rowlimit*]

*T* `| mv-expand ` [`bagexpansion=`(`bag` | `array`)] [*Name* `=`] *ArrayExpression* [`to typeof(`*Typename*`)`] [, [*Name* `=`] *ArrayExpression* [`to typeof(`*Typename*`)`] ...] [`limit` *Rowlimit*]

## <a name="arguments"></a>인수

* *ColumnName:* 결과적으로 명명된 열의 배열이 여러 행으로 확장됩니다. 
* *ArrayExpression:* 배열을 생성하는 식입니다. 이 양식을 사용하면 새 열이 추가되며 기존 열은 보존됩니다.
* *Name:* 새 열에 대한 이름입니다.
* *Typename:* `mv-apply` 연산자에 의해 생성되는 열의 형식이 되는 배열 요소의 기본 형식을 나타냅니다. 형식 적용 작업은 캐스트 전용이며, 구문 분석 또는 형식 변환을 포함하지 않습니다. 선언된 형식을 따르지 않는 배열 요소는 `null` 값이 됩니다.
* *RowLimit:* 각각의 원래 행에서 생성되는 최대 행 수입니다. 기본값은 2147483647입니다. 

  > [!NOTE]
  > `mvexpand`는 `mv-expand` 연산자의 레거시 형식 및 사용되지 않는 형식입니다. 레거시 버전의 기본 행 제한은 128입니다.

* *IndexColumnName:* `with_itemindex`가 지정된 경우 출력에는 원래 확장된 컬렉션에 있는 항목의 인덱스(0부터 시작)를 포함하는 추가 열(*IndexColumnName*)이 포함됩니다. 

## <a name="returns"></a>반환

명명된 열의 배열 또는 배열 식에 있는 각 값에 대한 여러 행.
여러 열 또는 식이 지정되면 병렬로 확장됩니다. 각 입력 행에 대해 가장 긴 확장 식에 있는 요소 수 만큼의 출력 행이 있습니다(짧은 목록은 null로 채워짐). 행의 값이 빈 배열인 경우 행은 아무 것도 확장되지 않습니다(결과 집합에 표시되지 않음). 그러나 행의 값이 배열이 아닌 경우 행은 결과 집합에 있는 그대로 유지됩니다. 

확장된 열은 언제나 동적 형식을 가집니다. 값을 계산하거나 집계하려는 경우 `todatetime()` 또는 `tolong()`와 같은 캐스트를 사용합니다.

속성 모음 확장의 두 가지 모드가 지원됩니다.
* `bagexpansion=bag` 또는 `kind=bag`: 속성 모음이 단일 항목 속성 모음으로 확장됩니다. 이 모드는 기본 확장입니다.
* `bagexpansion=array` 또는 `kind=array`: 속성 모음이 두 요소로 이루어진 `[`*key*`,`*value*`]` 배열 구조로 확장되며 키 및 값에 대한 균일한 액세스가 가능합니다(또한 예를 들어 속성 이름에 대해 고유 카운트 집계 실행). 

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

`b` 열은 `dynamic`으로 표시되고 `c`는 `int`로 표시됩니다.

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
