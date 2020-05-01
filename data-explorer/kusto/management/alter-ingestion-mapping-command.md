---
title: . 변경 수집 매핑-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 alter 수집 매핑에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: 2f43039ff3935edbb6e92627d2f96b1c411e1ffa
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/01/2020
ms.locfileid: "82617819"
---
# <a name="alter-ingestion-mapping"></a>.alter ingestion mapping

특정 테이블과 관련 된 기존 수집 매핑과 특정 형식 (전체 매핑 replace)을 변경 합니다.

**구문**

`.alter``table` *TableName* TableName `ingestion` *MappingKind* MappingKind `mapping` *MappingName* *MappingFormattedAsJson*

> [!NOTE]
> * 이 매핑은 명령의 일부로 전체 매핑을 지정 하는 대신 수집 명령을 통해 이름으로 참조할 수 있습니다.
> * _MappingKind_ 에 유효한 값은 `CSV`, `JSON`, `avro`, `parquet`및 `orc`입니다.

**예제** 
 
```kusto
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

| 속성     | 종류 | 매핑                                                                                                                                                                          |
|----------|------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| mapping1 | CSV  | [{"Name": "rownumber", "DataType": "int", "CsvDataType": null, "Ordinal": 0, "ConstValue": null}, {"Name": "rowguid", "DataType": "string", "CsvDataType": null, "서 수": 1, "ConstValue": null}] |
