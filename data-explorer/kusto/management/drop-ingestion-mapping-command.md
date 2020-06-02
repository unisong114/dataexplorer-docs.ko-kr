---
title: . 수집 매핑 삭제-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기에서 수집 매핑 삭제에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: 7454bd86a6ca2a835dc0515a9c8901a444259f12
ms.sourcegitcommit: 41cd88acc1fd79f320a8fe8012583d4c8522db78
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/02/2020
ms.locfileid: "84294528"
---
# <a name="drop-ingestion-mapping"></a>.drop ingestion mapping

데이터베이스에서 수집 매핑을 삭제 합니다.
 
`.drop``table` *TableName* `ingestion` *MappingKind* `mapping` *MappingName*   

**예제** 

```kusto
.drop table MyTable ingestion CSV mapping "Mapping1" 

.drop table MyTable ingestion json mapping "Mapping1" 
```
