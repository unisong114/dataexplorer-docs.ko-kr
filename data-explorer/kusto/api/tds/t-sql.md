---
title: T-sql-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 T-sql에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 1115414358a1025d4931484b81d6eda76109e6cd
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/13/2020
ms.locfileid: "83373523"
---
# <a name="t-sql-support"></a>T-sql 지원

[KQL (Kusto query language)](../../query/index.md) 는 기본 쿼리 언어입니다.
그러나 t-sql 지원은 KQL를 사용 하도록 쉽게 변환할 수 없는 도구에 유용 합니다.  
T-sql 지원은 SQL에 익숙한 사용자가 자주 사용 하는 경우에도 유용 합니다.

Kusto는 일부 언어 제한으로 T-sql 쿼리를 해석 하 고 실행할 수 있습니다.

> [!NOTE]
> Kusto는 DDL 명령을 지원 하지 않습니다. T-sql `select` 문만 지원 됩니다. T-sql과 관련 된 주요 차이점에 대 한 자세한 내용은 [SQL 알려진 문제](./sqlknownissues.md)를 참조 하세요.

## <a name="querying-from-kustoexplorer-with-t-sql"></a>T-sql을 사용 하 여 .Kusto 탐색기에서 쿼리

Kusto 탐색기 도구는 Kusto에 대 한 T-sql 쿼리를 지원 합니다.
Kusto 탐색기에 쿼리를 실행 하도록 지시 하려면 빈 T-sql 주석 줄 ()을 사용 하 여 쿼리를 시작 합니다. `--` 다음은 그 예입니다.

```sql
--
select * from StormEvents
```

## <a name="from-t-sql-to-kusto-query-language"></a>T-sql에서 Kusto 쿼리 언어로

Kusto는 T-sql 쿼리를 Kusto query language (KQL)로 변환 하는 기능을 지원 합니다. 이러한 번역을 통해 KQL를 보다 잘 이해할 수 있습니다.
일부 T-sql 문에서 동일한 KQL를 다시 가져오려면 `select` 쿼리 앞에를 추가 `explain` 합니다.

예를 들어 다음 T-sql 쿼리는 다음과 같습니다.

```sql
--
explain
select top(10) *
from StormEvents
order by DamageProperty desc
```

다음 출력을 생성 합니다.

```kusto
StormEvents
| sort by DamageProperty desc nulls first
| take 10
```
