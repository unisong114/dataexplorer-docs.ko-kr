---
title: Kusto IngestionTime 정책 관리-Azure 데이터 탐색기
description: Azure 데이터 탐색기의 IngestionTime 정책 명령에 익숙해져야 합니다. 수집 시간에 액세스 하는 방법 및이 정책을 설정 및 해제 하는 방법을 참조 하세요.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 2e1671373547a4ed069d67bc9153248f23bc3b5e
ms.sourcegitcommit: 3d9b4c3c0a2d44834ce4de3c2ae8eb5aa929c40f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/13/2020
ms.locfileid: "92003107"
---
# <a name="ingestiontime-policy-command"></a>ingestiontime 정책 명령

IngestionTime 정책은 테이블에 설정 되는 선택적 정책입니다 (기본적으로 사용 하도록 설정 됨).
테이블에 레코드를 수집 하는 대략적인 시간을 제공 합니다.

수집 시간 값은 쿼리 시 함수를 사용 하 여 액세스할 수 있습니다 `ingestion_time()` .

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