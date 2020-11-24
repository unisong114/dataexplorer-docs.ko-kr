---
title: mv 확장 연산자-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 mv 확장 연산자에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2019
ms.localizationpriority: high
ms.openlocfilehash: 3324cfe658b2eb29c54ff8a3d44ed660ec13ead5
ms.sourcegitcommit: 4e811d2f50d41c6e220b4ab1009bb81be08e7d84
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2020
ms.locfileid: "95512726"
---
# <a name="mv-expand-operator"></a>mv-expand 연산자

다중 값 배열 또는 속성 모음을 확장 합니다.

`mv-expand` 는 컬렉션의 각 값이 개별 행을 가질 수 있도록 [동적](./scalar-data-types/dynamic.md)으로 형식화 된 배열 또는 속성 모음 열에 적용 됩니다. 확장된 행의 다른 열은 모두 중복됩니다. 

## <a name="syntax"></a>구문

*T* `| mv-expand ` [ `bagexpansion=` ( `bag`  |  `array` )] [ `with_itemindex=` *indexcolumnname*] *ColumnName* [ `,` *columnname* ...] [ `limit` *rowlimit*]

*T* `| mv-expand ` [ `bagexpansion=` ( `bag`  |  `array` )] [*Name* `=` ] *arrayexpression* [ `to typeof(` *typename* `)` ] [, [*Name* `=` ] *arrayexpression* [ `to typeof(` *typename* `)` ] ...] [ `limit` *rowlimit*]

## <a name="arguments"></a>인수

* *ColumnName:* 결과적으로 명명된 열의 배열이 여러 행으로 확장됩니다. 
* *ArrayExpression:* 배열을 생성하는 식입니다. 이 양식을 사용하면 새 열이 추가되며 기존 열은 보존됩니다.
* *Name:* 새 열에 대한 이름입니다.
* 형식 *이름:* 연산자가 생성 하는 열의 형식이 되는 배열의 요소에 대 한 내부 형식을 나타냅니다 `mv-apply` . 형식 적용 작업은 캐스트 전용 이며 구문 분석 또는 형식 변환이 포함 되지 않습니다. 선언 된 형식을 따르지 않는 배열 요소는 값이 됩니다 `null` .
* *RowLimit:* 각각의 원래 행에서 생성되는 최대 행 수입니다. 기본값은 2147483647입니다. 

  > [!Note]
  > 연산자의 레거시 및 사용 되지 않는 형식에는 `mvexpand` 기본 행 제한인 128이 있습니다.

* *인덱스 columnname:* `with_itemindex` 이 지정 된 경우 출력에는 원래 확장 된 컬렉션에 있는 항목의 인덱스 (0부터 시작)를 포함 하는 추가 열 (명명 된 *indexcolumnname*)이 포함 됩니다. 

## <a name="returns"></a>반환

명명 된 열 또는 배열 식에 있는 배열의 각 값에 대 한 여러 행
여러 개의 열 또는 식을 지정 하는 경우 병렬로 확장 됩니다. 각 입력 행에 대해 가장 긴 확장 식에 있는 요소 수 만큼의 출력 행이 있습니다. 짧은 목록은 null로 채워집니다. 행의 값이 빈 배열인 경우에는 행이 nothing (결과 집합에 표시 되지 않음)으로 확장 됩니다. 그러나 행의 값이 배열이 아닌 경우 행은 결과 집합에 그대로 유지 됩니다. 

확장된 열은 언제나 동적 형식을 가집니다. 값을 계산하거나 집계하려는 경우 `todatetime()` 또는 `tolong()`와 같은 캐스트를 사용합니다.

속성 모음 확장의 두 가지 모드가 지원됩니다.
* `bagexpansion=bag`: 속성 모음이 단일 항목 속성 모음으로 확장됩니다. 이 모드는 기본 확장입니다.
* `bagexpansion=array`: 속성 모음은 두 요소 `[` *키* `,` *값* `]` 배열 구조로 확장 되어 키와 값에 대 한 균일 한 액세스를 허용 합니다 (예: 속성 이름에 대 한 고유 카운트 집계 실행). 

## <a name="examples"></a>예

### <a name="single-column"></a>단일 열

단일 열의 간단한 확장입니다.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
 ```kusto
datatable (a:int, b:dynamic)[1,dynamic({"prop1":"a", "prop2":"b"})]
| mv-expand b 
```

|a|b|
|---|---|
|1|{"prop1": "a"}|
|1|{"prop2": "b"}|

### <a name="zipped-two-columns"></a>압축 두 열

두 열을 확장 하면 먼저 해당 열을 ' zip ' 한 다음 확장 합니다.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
datatable (a:int, b:dynamic, c:dynamic)[1,dynamic({"prop1":"a", "prop2":"b"}), dynamic([5, 4, 3])]
| mv-expand b, c
```

|a|b|c|
|---|---|---|
|1|{"prop1": "a"}|5|
|1|{"prop2": "b"}|4|
|1||3|

### <a name="cartesian-product-of-two-columns"></a>두 열의 데카르트 곱

두 열을 확장 하는 데카르트 곱을 얻으려면 다른 열 다음에 하나씩을 확장 합니다.

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
|1|{"prop1": "a"}|5|
|1|{"prop1": "a"}|6|
|1|{"prop2": "b"}|5|
|1|{"prop2": "b"}|6|

### <a name="convert-output"></a>출력 변환

Mv-expand의 출력을 특정 형식으로 강제 적용 하려면 (기본값은 동적) 다음을 사용 합니다 `to typeof` .

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

가로 제공 되는 `b` 동안 열이 표시 됩니다 `dynamic` `c` `int` .

### <a name="using-with_itemindex"></a>With_itemindex 사용

다음을 사용 하 여 배열 확장 `with_itemindex` :

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
 
## <a name="see-also"></a>참조

* 더 많은 예제는 [시간별 라이브 활동의 차트 수](./samples.md#chart-concurrent-sessions-over-time) 를 참조 하세요.
* [mv-](./mv-applyoperator.md) 연산자를 적용 합니다.
* mv 확장의 반대 함수인 [make_list ()를 요약](makelist-aggfunction.md)합니다.
* 속성 모음 키를 사용 하 여 동적 JSON 개체를 열로 확장 하는 [bag_unpack ()](bag-unpackplugin.md) 플러그 인입니다.
