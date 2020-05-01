---
title: . 수집 매핑 표시-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기에서 수집 매핑 표시에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: 711861a07896b7bdc4cf57bebbf1cdd0e01d064a
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/01/2020
ms.locfileid: "82617173"
---
# <a name="show-ingestion-mappings"></a>. 수집 매핑 표시

수집 매핑을 표시 합니다 (모두 또는 이름으로 지정한 하나).

* `.show``table` *TableName* TableName `ingestion` *MappingKind*  `mappings`

* `.show``table` *TableName* TableName `ingestion` *MappingKind* MappingKind`mapping` *MappingName*   

모든 매핑 종류의 모든 수집 매핑을 표시 합니다.

* `.show``table` *TableName*`ingestion`  `mapping`
 
**예제** 
 
```kusto
.show table MyTable ingestion csv mapping "Mapping1" 

.show table MyTable ingestion csv mappings 

.show table MyTable ingestion mappings 
```

**예제 출력**

| 속성     | 종류 | 매핑     |
|----------|------|-------------|
| mapping1 | CSV  | [{"Name": "rownumber", "DataType": "int", "CsvDataType": null, "Ordinal": 0, "ConstValue": null}, {"Name": "rowguid", "DataType": "string", "CsvDataType": null, "서 수": 1, "ConstValue": null}] |
