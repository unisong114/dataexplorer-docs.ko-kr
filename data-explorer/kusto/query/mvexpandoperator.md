---
title: mv 확장 연산자-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 mv 확장 연산자에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2019
ms.openlocfilehash: a0d19f3466381bef3848365c2af27109e9699087
ms.sourcegitcommit: 733bde4c6bc422c64752af338b29cd55a5af1f88
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/13/2020
ms.locfileid: "83271318"
---
# <a name="mv-expand-operator"></a>mv-expand 연산자

다중 값 배열 또는 속성 모음을 확장 합니다.

`mv-expand`는 [동적](./scalar-data-types/dynamic.md)형식의 열에 적용 되므로 컬렉션의 각 값은 별도의 행을 가져옵니다. 확장된 행의 다른 열은 모두 중복됩니다. 

**구문**

*T* `| mv-expand ` [ `bagexpansion=` ( `bag`  |  `array` )] [ `with_itemindex=` *indexcolumnname*] *ColumnName* [ `,` *columnname* ...] [ `limit` *rowlimit*]

*T* `| mv-expand ` [ `bagexpansion=` ( `bag`  |  `array` )] [*Name* `=` ] *arrayexpression* [ `to typeof(` *typename* `)` ] [, [*Name* `=` ] *arrayexpression* [ `to typeof(` *typename* `)` ] ...] [ `limit` *rowlimit*]

**인수**

* *ColumnName:* 결과적으로 명명된 열의 배열이 여러 행으로 확장됩니다. 
* *ArrayExpression:* 배열을 생성하는 식입니다. 이 양식을 사용하면 새 열이 추가되며 기존 열은 보존됩니다.
* *Name:* 새 열에 대한 이름입니다.
* 형식 *이름:* 연산자가 생성 하는 열의 형식이 되는 배열의 요소에 대 한 내부 형식을 나타냅니다.
    이 형식을 따르지 않는 배열의 값은 변환 되지 않습니다. 그 대신 값이 사용 됩니다 `null` .
* *RowLimit:* 각각의 원래 행에서 생성되는 최대 행 수입니다. 기본값은 2147483647입니다. 
*참고*: 연산자의 레거시 및 사용 되지 않는 형식 `mvexpand` 에는 기본 행 제한인 128이 있습니다.
* *인덱스 columnname:* `with_itemindex`이 지정 된 경우 출력에는 원래 확장 된 컬렉션에 있는 항목의 인덱스 (0부터 시작)를 포함 하는 추가 열 (명명 된 *indexcolumnname*)이 포함 됩니다. 

**반환**

명명된 열의 배열 또는 배열 식에 있는 각 값에 대한 여러 행.
여러 개의 열 또는 식이 지정 된 경우 병렬로 확장 되므로 각 입력 행에 대해 가장 긴 확장 식에 있는 요소 수 만큼의 출력 행이 포함 됩니다 (짧은 목록은 null로 패딩 됨). 행의 값이 빈 배열인 경우에는 행이 nothing (결과 집합에 표시 되지 않음)으로 확장 됩니다. 행의 값이 배열이 아니면 행은 결과 집합에 그대로 유지 됩니다. 

확장된 열은 언제나 동적 형식을 가집니다. 값을 계산하거나 집계하려는 경우 `todatetime()` 또는 `tolong()`와 같은 캐스트를 사용합니다.

속성 모음 확장의 두 가지 모드가 지원됩니다.
* `bagexpansion=bag`: 속성 모음이 단일 항목 속성 모음으로 확장됩니다. 이는 기본 확장입니다.
* `bagexpansion=array`: 속성 모음은 두 요소 `[` *키* `,` *값* `]` 배열 구조로 확장 되어 키와 값에 대 한 일관 된 액세스를 허용 합니다 (예: 속성 이름에 대 한 고유 카운트 집계 실행). 

**예**

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


두 열을 확장 하면 먼저 해당 열을 ' zip ' 한 다음 확장 합니다.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
datatable (a:int, b:dynamic, c:dynamic)[1,dynamic({"prop1":"a", "prop2":"b"}), dynamic([5])]
| mv-expand b, c 
```

|a|b|c|
|---|---|---|
|1|{"prop1": "a"}|5|
|1|{"prop2": "b"}||

두 열을 확장 하는 데카르트 곱을 얻으려면 다른 열 다음에 하나씩을 확장 합니다.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
datatable (a:int, b:dynamic, c:dynamic)[1,dynamic({"prop1":"a", "prop2":"b"}), dynamic([5])]
| mv-expand b 
| mv-expand c
```

|a|b|c|
|---|---|---|
|1|{"prop1": "a"}|5|
|1|{"prop2": "b"}|5|


다음을 사용 하 여 배열 확장 `with_itemindex` :

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
range x from 1 to 4 step 1 
| summarize x = make_list(x) 
| mv-expand with_itemindex=Index  x 
```

|x|인덱스|
|---|---|
|1|0|
|2|1|
|3|2|
|4|3|


**추가 예제**

[시간별 라이브 활동의 차트 수](./samples.md#concurrent-activities)를 참조 하세요.

**참고 항목**

- [mv-](./mv-applyoperator.md) 연산자를 적용 합니다.
- 반대 기능을 수행 하는 [make_list ()를 요약](makelist-aggfunction.md) 합니다.
- 속성 모음 키를 사용 하 여 동적 JSON 개체를 열로 확장 하는 [bag_unpack ()](bag-unpackplugin.md) 플러그 인입니다.
