---
title: T-SQL - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 T-SQL에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: d262d8b7587296c02a2a31d850919af0931bcde6
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81523412"
---
# <a name="t-sql"></a>T-SQL

Kusto 서비스는 일부 언어 제한으로 T-SQL 쿼리를 해석하고 실행할 수 있습니다.
[Kusto 쿼리 언어는](../../query/index.md) Kusto에 대 한 기본 언어, 이러한 지원은 쉽게 기본 쿼리 언어를 사용 하 여 변환할 수 없는 기존 도구에 대 한 유용 하 고 SQL에 익숙한 사람들에 의해 Kusto의 캐주얼 사용.

> [!NOTE]
> Kusto는 이러한 방식으로 DDL 명령을 지원하지 않으며 T-SQL `SELECT` 문만 지원됩니다. [T-SQL과](./sqlknownissues.md) 관련하여 SQL Server와 Kusto 간의 주요 차이점에 대한 자세한 내용은 SQL 알려진 문제를 참조하십시오.

## <a name="querying-kusto-from-kustoexplorer-with-t-sql"></a>Kusto.Explorer에서 T-SQL로 쿠스토 쿼리

Kusto.Explorer 도구는 Kusto에 T-SQL 쿼리를 보내는 것을 지원합니다.
Kusto.Explorer가 이 모드에서 쿼리를 실행하도록 지시하려면 쿼리에 빈 T-SQL 주석 줄을 미리 준비합니다. 다음은 그 예입니다.

```sql
--
select * from StormEvents
```

## <a name="from-t-sql-to-kusto-query-language"></a>T-SQL에서 Kusto 쿼리 언어로

Kusto는 T-SQL 쿼리를 Kusto 쿼리 언어로 변환하는 것을 지원합니다. 예를 들어 Kusto 쿼리 언어를 더 잘 이해하려는 SQL에 익숙한 사람이 사용할 수 있습니다. 일부 T-SQL `SELECT` 문에 해당하는 Kusto 쿼리 언어를 다시 `EXPLAIN` 얻으려면 쿼리 앞에 추가하십시오.

예를 들어 다음 T-SQL 쿼리는 다음과 같은 경우입니다.

```sql
--
explain
select top(10) *
from StormEvents
order by DamageProperty desc
```

이 출력을 생성합니다.

```kusto
StormEvents
| sort by DamageProperty desc nulls first
| take 10
```