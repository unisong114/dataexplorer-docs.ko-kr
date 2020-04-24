---
title: .alter 수집 매핑 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기에서 .alter 수집 매핑에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: 5343d55fadafce552c5d837e5eb50763ccf45a4c
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81522409"
---
# <a name="alter-ingestion-mapping"></a>.alter ingestion mapping

특정 테이블 및 특정 형식(전체 매핑 바꾸기)과 연결된 기존 인식 매핑을 변경합니다.

**구문**

`.alter``table` *테이블 이름* `ingestion` *매핑Kind* `mapping` *매핑이름* *매핑formattedAsJson*

> [!NOTE]
> * 이 매핑은 명령의 일부로 전체 매핑을 지정하는 대신 인기 명령으로 이름으로 참조할 수 있습니다.
> * _매핑Kind에_ 대한 유효한 `CSV` `JSON`값은 다음과 `orc`같습니다. `avro` `parquet`

**예제** 
 
```
.alter table MyTable ingestion csv mapping "Mapping1"
'['
'   { "column" : "rownumber", "DataType":"int", "Properties":{"Ordinal":"0"}},'
'   { "column" : "rowguid", "DataType":"string", "Properties":{"Ordinal":"1"} }'
']'

.alter table MyTable ingestion json mapping "Mapping1"
'['
'   { "column" : "rownumber", "Properties":{"Path":"$.rownumber"}},'
'   { "column" : "rowguid", "Properties":{"Path":"$.rowguid"}}'
']'
```
**예제 출력**

| 이름     | 종류 | 매핑                                                                                                                                                                          |
|----------|------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 매핑1 | CSV  | [{"Name":"행 번호","DataType":"int","CsvDataType","int","CsvDataType":null","ConstValue":null},{"이름":"rowguid","데이터 타입":"문자열","CsvDataType":null":1,"ConstValue":null}] |