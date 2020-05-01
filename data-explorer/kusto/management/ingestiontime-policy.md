---
title: Kusto IngestionTime 정책 관리-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 IngestionTime 정책에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 907c6ddf84d772f800fce45d3c1245bbd11b0c85
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/01/2020
ms.locfileid: "82616459"
---
# <a name="ingestiontime-policy"></a>IngestionTime 정책

IngestionTime 정책은 테이블에 설정 되는 선택적 정책입니다 (기본적으로 사용 하도록 설정 됨).
테이블에 레코드를 수집 하는 대략적인 시간을 제공 합니다.

수집 시간 값은 쿼리 시 함수를 사용 하 `ingestion_time()` 여 액세스할 수 있습니다.

```kusto
T | extend ingestionTime = ingestion_time()
```

정책을 사용 하거나 사용 하지 않도록 설정 하려면:
```kusto
.alter table table_name policy ingestiontime true
```

여러 테이블의 정책을 설정 하거나 해제 하려면:
```kusto
.alter tables (table_name [, ...]) policy ingestiontime true
```

정책을 보려면 다음을 수행 합니다.
```kusto
.show table table_name policy ingestiontime  

.show table * policy ingestiontime  
```

정책을 삭제 하려면 (비활성화와 같음):
```kusto
.delete table table_name policy ingestiontime  
```