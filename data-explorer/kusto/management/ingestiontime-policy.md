---
title: 인비시타임 정책 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 인시션Time 정책에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: c42da570b961595be1fbcae352fe121d8b6f59ea
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81520896"
---
# <a name="ingestiontime-policy"></a>IngestionTime 정책

인제션Time 정책은 테이블에 설정된 선택적 정책입니다(기본적으로 활성화).
레코드를 테이블로 통합하는 대략적인 시간을 제공합니다.

인베이션 시간 값은 함수를 사용하여 `ingestion_time()` 쿼리 시간에 액세스할 수 있습니다.

```kusto
T | extend ingestionTime = ingestion_time()
```

정책을 사용/사용하지 않으려면 다음을 수행하십시오.
```kusto
.alter table table_name policy ingestiontime true
```

여러 테이블의 정책을 활성화/비활성화하려면 다음을 수행하십시오.
```kusto
.alter tables (table_name [, ...]) policy ingestiontime true
```

정책을 보려면 다음을 수행하십시오.
```kusto
.show table table_name policy ingestiontime  

.show table * policy ingestiontime  
```

정책을 삭제하려면(비활성화와 동일):
```kusto
.delete table table_name policy ingestiontime  
```