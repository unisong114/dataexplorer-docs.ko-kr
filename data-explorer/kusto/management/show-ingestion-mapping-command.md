---
title: .show 수집 매핑 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기에서 .show 수집 매핑에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: 91990fe40664ae89d69357812b0def2c7288eb7d
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81519825"
---
# <a name="show-ingestion-mappings"></a>.show 인비온 매핑

인기 매핑(이름으로 지정된 모든 또는 하나)을 표시합니다.

* `.show``table` *테이블 이름* `ingestion` *매핑Kind*  `mappings`

* `.show``table` *테이블 이름* `ingestion` *매핑Kind* `mapping` *매핑이름*   

모든 매핑 종류의 모든 인기 매핑 표시:

* `.show``table` *테이블 이름*`ingestion`  `mapping`
 
**예제** 
 
```
.show table MyTable ingestion csv mapping "Mapping1" 

.show table MyTable ingestion csv mappings 

.show table MyTable ingestion mappings 
```

**예제 출력**

| 이름     | 종류 | 매핑     |
|----------|------|-------------|
| 매핑1 | CSV  | [{"Name":"행 번호","DataType":"int","CsvDataType","int","CsvDataType":null","ConstValue":null},{"이름":"rowguid","데이터 타입":"문자열","CsvDataType":null":1,"ConstValue":null}] |