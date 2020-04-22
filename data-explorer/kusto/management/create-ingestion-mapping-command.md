---
title: .create 수집 매핑 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기에서 .create 수집 매핑에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: 10e656b074516ad8b0018e627d9904251aebbf10
ms.sourcegitcommit: e94be7045d71a0435b4171ca3a7c30455e6dfa57
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81744501"
---
# <a name="create-ingestion-mapping"></a>.create ingestion mapping

특정 테이블 및 특정 형식과 연관된 인기 맵을 만듭니다.

**구문**

`.create``table` *테이블 이름* `ingestion` *매핑Kind* `mapping` *매핑이름* *매핑formattedAsJson*

> [!NOTE]
> * 생성되면 전체 매핑을 명령의 일부로 지정하는 대신 인베이션 명령에서 매핑이름을 참조할 수 있습니다.
> * _매핑Kind에_ 대한 유효한 `CSV` `JSON`값은 `parquet`다음과 같습니다. `avro``orc`
> * 동일한 이름의 매핑이 테이블에 이미 있는 경우:
>    * `.create`실패합니다.
>    * `.create-or-alter`기존 매핑이 변경됩니다.
 
**예제** 
 
```kusto
.create table MyTable ingestion csv mapping "Mapping1"
'['
'   { "column" : "rownumber", "DataType":"int", "Properties":{"Ordinal":"0"}},'
'   { "column" : "rowguid", "DataType":"string", "Properties":{"Ordinal":"1"}}'
']'

.create-or-alter table MyTable ingestion json mapping "Mapping1"
'['
'    { "column" : "rownumber", "datatype" : "int", "Properties":{"Path":"$.rownumber"}},'
'    { "column" : "rowguid", "Properties":{"Path":"$.rowguid"}}'
']'
```

**예제 출력**

| 이름     | 종류 | 매핑                                                                                                                                                                          |
|----------|------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 매핑1 | CSV  | [{"Name":"행 번호","DataType":"int","CsvDataType","int","CsvDataType":null","ConstValue":null},{"이름":"rowguid","데이터 타입":"문자열","CsvDataType":null":1,"ConstValue":null}] |
