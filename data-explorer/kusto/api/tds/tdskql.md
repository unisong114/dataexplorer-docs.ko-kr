---
title: TDS를 통해 KQL - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기에서 TDS를 통해 KQL에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/09/2019
ms.openlocfilehash: 364db99141c528f4a822692124ebb870d7315bca
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81523310"
---
# <a name="kql-over-tds"></a>TDS를 통한 KQL

Kusto는 기본 [KQL](../../query/index.md) 쿼리 언어로 작성된 쿼리를 실행하기 위해 TDS 끝점을 활용할 수 있습니다. 이 기능은 Kusto쪽으로 더 매끄러운 migiration을 제공합니다. 예를 들어 KQL 쿼리를 사용하여 Kusto를 쿼리하는 SSIS 작업을 만들 수 있습니다.

## <a name="executing-kusto-stored-functions"></a>쿠스토 저장 기능 실행

Kusto는 SQL 저장 [프로시저를 호출하는](../../query/schema-entities/stored-functions.md) 경우와 유사하게 저장된 함수를 실행할 수 있습니다.

예를 들어, 저장된 함수 MyFunction:

|이름 |매개 변수|본문|폴더|닥스트링 (것)과 함께
|---|---|---|---|---
|마이 기능 |(myLimit: 롱)| {스톰이벤트 &#124; 제한 myLimit}|마이 폴더|매개 변수가 있는 데모 기능||

다음과 같이 호출할 수 있습니다.

```csharp
  using (var connection = new SqlConnection(csb.ToString()))
  {
    await connection.OpenAsync();
    using (var command = new SqlCommand("kusto.MyFunction", connection))
    {
      command.CommandType = CommandType.StoredProcedure;
      var parameter = new SqlParameter("mylimit", SqlDbType.Int);
      command.Parameters.Add(parameter);
      parameter.Value = 3;
      using (var reader = await command.ExecuteReaderAsync())
      {
        // Read the response.
      }
    }
  }
```

Kusto 저장된 함수와 에뮬레이트된 `kusto`SQL 시스템 저장 프로시저를 구분하기 위해 저장된 함수를 명시적 스키마로 호출해야 합니다.

Kusto 에 저장된 함수는 SQL 테이블 형식 함수와 마찬가지로 T-SQL에서 호출할 수도 있습니다.

```sql
SELECT * FROM kusto.MyFunction(10)
```

최적화된 KQL 쿼리를 만들고 저장된 함수에 캡슐화하여 T-SQL 쿼리 코드를 최소화하는 것이 좋습니다.

## <a name="executing-kql-query"></a>KQL 쿼리 실행

SQL 서버와 `sp_executesql`마찬가지로 Kusto는 `sp_execute_kql` 매개 변수화된 쿼리를 포함하여 [KQL](../../query/index.md) 쿼리를 실행하기 위한 저장 프로시저를 도입했습니다.

첫 `sp_execute_kql` 번째 매개 변수는 KQL 쿼리입니다. 추가 매개 변수를 도입할 수 있으며 [쿼리 매개 변수처럼 작동합니다.](../../query/queryparametersstatement.md)

다음은 그 예입니다.

```csharp
  using (var connection = new SqlConnection(csb.ToString()))
  {
    await connection.OpenAsync();
    using (var command = new SqlCommand("sp_execute_kql", connection))
    {
      command.CommandType = CommandType.StoredProcedure;
      var query = new SqlParameter("@kql_query", SqlDbType.NVarChar);
      command.Parameters.Add(query);
      var parameter = new SqlParameter("mylimit", SqlDbType.Int);
      command.Parameters.Add(parameter);
      query.Value = "StormEvents | limit myLimit";
      parameter.Value = 3;
      using (var reader = await command.ExecuteReaderAsync())
      {
        // Read the response.
      }
    }
  }
```

매개 변수 형식은 프로토콜을 통해 설정되기 때문에 TDS를 통해 호출 할 때 매개 변수를 선언 할 필요가 없습니다.