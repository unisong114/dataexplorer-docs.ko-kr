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
ms.openlocfilehash: 69ab4849d67d7cc7507bab075b0111fbd8ec95e7
ms.sourcegitcommit: 8e097319ea989661e1958efaa1586459d2b69292
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/15/2020
ms.locfileid: "84780612"
---
# <a name="drop-ingestion-mapping"></a>.drop ingestion mapping

데이터베이스에서 수집 매핑을 삭제 합니다.
 
`.drop``table` *TableName* `ingestion` *MappingKind* `mapping` *MappingName*   

**예제** 

```kusto
.drop table MyTable ingestion csv mapping "Mapping1" 

.drop table MyTable ingestion json mapping "Mapping1" 
```
