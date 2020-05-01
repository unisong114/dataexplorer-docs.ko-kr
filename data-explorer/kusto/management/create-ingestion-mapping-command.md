---
title: . 수집 매핑 만들기-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기에서 수집 매핑 만들기에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: 84ab277f5b0d4d1b2e09d31fb7c1254786affe6d
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/01/2020
ms.locfileid: "82617734"
---
# <a name="create-ingestion-mapping"></a>.create ingestion mapping

특정 테이블 및 특정 형식과 연결 된 수집 매핑을 만듭니다.

**구문**

`.create``table` *TableName* TableName `ingestion` *MappingKind* MappingKind `mapping` *MappingName* *MappingFormattedAsJson*

> [!NOTE]
> * 생성 된 매핑은 명령의 일부로 전체 매핑을 지정 하는 대신 수집 명령에서 이름으로 참조할 수 있습니다.
> * _MappingKind_ 에 유효한 값은, `CSV` `JSON`, `avro`, `parquet`및입니다.`orc`
> * 테이블에 대해 이름이 같은 매핑이 이미 있는 경우:
>    * `.create`실패 합니다.
>    * `.create-or-alter`기존 매핑을 변경 합니다.
 
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

| 속성     | 종류 | 매핑                                                                                                                                                                          |
|----------|------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| mapping1 | CSV  | [{"Name": "rownumber", "DataType": "int", "CsvDataType": null, "Ordinal": 0, "ConstValue": null}, {"Name": "rowguid", "DataType": "string", "CsvDataType": null, "서 수": 1, "ConstValue": null}] |

## <a name="next-steps"></a>다음 단계
수집 매핑에 대 한 자세한 내용은 [데이터 매핑](mappings.md)을 참조 하세요.
