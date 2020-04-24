---
title: mv 확장 연산자 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기에서 mv 확장 연산자에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2019
ms.openlocfilehash: 01a4b56155d1c29727670b4e827cb7b9968ef7a9
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81512294"
---
# <a name="mv-expand-operator"></a>mv-expand 연산자

다중 값 배열 또는 속성 백을 확장합니다.

`mv-expand`은 [동적](./scalar-data-types/dynamic.md)-형식의 열에 적용되므로 컬렉션의 각 값이 별도의 행을 가져옵니다. 확장된 행의 다른 열은 모두 중복됩니다. 

**구문**

*T* `| mv-expand ` `bagexpansion=`[()]`bag` | `array``with_itemindex=`*[색인열 이름]* *열 이름* [`,` 열 *이름* ...] [`limit` *행 제한*]

*T* `| mv-expand ` `bagexpansion=`[()]`bag` | `array`*[이름] [이름]* `=` *배열식* [`to typeof(`*입력명*`)`]*[이름]* `=` *배열식식* [`to typeof(`*타이핑명*`)`] ...] [`limit` *행 제한*]

**인수**

* *ColumnName:* 결과적으로 명명된 열의 배열이 여러 행으로 확장됩니다. 
* *ArrayExpression:* 배열을 생성하는 식입니다. 이 양식을 사용하면 새 열이 추가되며 기존 열은 보존됩니다.
* *Name:* 새 열에 대한 이름입니다.
* *형식 이름:* 연산자가 생성한 열의 형식이 되는 배열 요소의 기본 형식을 나타냅니다.
    이 형식을 따르지 않는 배열의 값은 변환되지 않습니다. 오히려, 그들은 `null` 값을 취할 것입니다.
* *RowLimit:* 각각의 원래 행에서 생성되는 최대 행 수입니다. 기본값은 2147483647입니다. 
*참고*: 연산자의 `mvexpand` 레거시 및 사용되지 않는 형식에는 기본 행 제한이 128입니다.
* *색인열 이름:* `with_itemindex` 지정하면 출력에는 원래 확장된 컬렉션의 항목의 인덱스(0부터 시작)가 포함된 추가 *열(IndexColumnName)이*포함됩니다. 

**반환**

명명된 열의 배열 또는 배열 식에 있는 각 값에 대한 여러 행.
여러 열이나 식이 지정되면 각 입력 행에 대해 가장 긴 확장식의 요소가 있는 만큼 많은 출력 행이 있으므로 병렬로 확장됩니다(짧은 목록은 null로 패딩됩니다). 행의 값이 빈 배열인 경우 행이 아무 것도 확장되지 않습니다(결과 집합에 표시되지 않음). 행의 값이 배열이 아닌 경우 행은 결과 집합의 대로 유지됩니다. 

확장된 열은 언제나 동적 형식을 가집니다. 값을 계산하거나 집계하려는 경우 `todatetime()` 또는 `tolong()`와 같은 캐스트를 사용합니다.

속성 모음 확장의 두 가지 모드가 지원됩니다.
* `bagexpansion=bag`: 속성 모음이 단일 항목 속성 모음으로 확장됩니다. 이는 기본 확장입니다.
* `bagexpansion=array`: 속성 백은 키와 `[`값에 대한 균일한 액세스를 허용하는 두 요소 *키*`,`*값* `]` 배열 구조로 확장되어 속성 이름에 대한 고유 개수 집계를 실행합니다. 

**예**

단일 열의 간단한 확장:
 ```kusto
datatable (a:int, b:dynamic)[1,dynamic({"prop1":"a", "prop2":"b"})]
| mv-expand b 
```

|a|b|
|---|---|
|1|{"prop1":"a"}|
|1|{"prop2":"b"}|


두 열을 확장하면 먼저 해당 열을 'zip'으로 확장한 다음 확장합니다.

```kusto
datatable (a:int, b:dynamic, c:dynamic)[1,dynamic({"prop1":"a", "prop2":"b"}), dynamic([5])]
| mv-expand b, c 
```

|a|b|c|
|---|---|---|
|1|{"prop1":"a"}|5|
|1|{"prop2":"b"}||

두 개의 열을 확장하는 카르테시안 제품을 얻으려면 차례로 확장하십시오.
```kusto
datatable (a:int, b:dynamic, c:dynamic)[1,dynamic({"prop1":"a", "prop2":"b"}), dynamic([5])]
| mv-expand b 
| mv-expand c
```

|a|b|c|
|---|---|---|
|1|{"prop1":"a"}|5|
|1|{"prop2":"b"}|5|


배열의 확장 `with_itemindex`:
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


**더 많은 예**

[시간 지남에 따라 라이브 활동의 차트 수를](./samples.md#concurrent-activities)참조하십시오.

**참고 항목**

- [mv 적용](./mv-applyoperator.md) 연산자.
- 반대 기능을 수행하는 [make_list() 를 요약합니다.](makelist-aggfunction.md)
- [bag_unpack()](bag-unpackplugin.md) 속성 가방 키를 사용 하 여 열로 동적 JSON 개체를 확장 하기 위한 플러그인입니다.