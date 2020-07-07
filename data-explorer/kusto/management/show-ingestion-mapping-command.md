---
title: . 수집 매핑 표시-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 수집 매핑 표시에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: 4a225c7d9cb1c5f99434c4595cbd798d020cfd9f
ms.sourcegitcommit: b08b1546122b64fb8e465073c93c78c7943824d9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/06/2020
ms.locfileid: "85967437"
---
# <a name="show-ingestion-mapping"></a>.show ingestion mapping

수집 매핑을 표시 합니다 (모두 또는 이름으로 지정한 하나).

* `.show``table` *TableName* `ingestion` *MappingKind*  `mappings`

* `.show``table` *TableName* `ingestion` *MappingKind* `mapping` *MappingName*   

모든 매핑 유형에 서 모든 수집 매핑을 표시 합니다.

* `.show``table` *TableName*`ingestion`  `mapping`
 
**예제** 
 
```kusto
.show table MyTable ingestion csv mapping "Mapping1" 

.show table MyTable ingestion csv mappings 

.show table MyTable ingestion mappings 
```

**샘플 출력**

| Name     | 종류 | 매핑     |
|----------|------|-------------|
| mapping1 | CSV  | `[{"Name":"rownumber","DataType":"int","CsvDataType":null,"Ordinal":0,"ConstValue":null},{"Name":"rowguid","DataType":"string","CsvDataType":null,"Ordinal":1,"ConstValue":null}]` |
