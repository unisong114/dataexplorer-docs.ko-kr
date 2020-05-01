---
title: TDS KQL-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 TDS를 통한 KQL을 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/09/2019
ms.openlocfilehash: 2c4443c0a9301dbc6bb3e65392163da0cc237f74
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/01/2020
ms.locfileid: "82617887"
---
# <a name="kql-over-tds"></a>TDS를 통한 KQL

Kusto는 TDS 끝점이 네이티브 [KQL](../../query/index.md) 쿼리 언어로 작성 된 쿼리를 실행할 수 있도록 합니다. 이 기능은 Kusto로의 원활한 마이그레이션을 가능 하 게 합니다. 예를 들어 KQL 쿼리를 사용 하 여 Kusto를 쿼리 하는 SSIS 작업을 만들 수 있습니다.

## <a name="executing-kusto-stored-functions"></a>Kusto 저장 함수 실행

Kusto는 SQL 저장 프로시저 호출과 같이 [저장 된 함수](../../query/schema-entities/stored-functions.md) 를 실행할 수 있도록 합니다.

예를 들어 저장 된 함수 MyFunction는 다음과 같습니다.

|속성 |매개 변수|본문|폴더|DocString
|---|---|---|---|---
|MyFunction |(myLimit: long)| {StormEvents &#124; 제한 myLimit}|MyFolder|매개 변수가 있는 Demo 함수||

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

> [! 참고:]는 이라는 `kusto`명시적 스키마를 사용 하 여 저장 된 함수를 호출 하 여 kusto 저장 된 함수와 에뮬레이트된 SQL 시스템 저장 프로시저를 구분 합니다.

SQL 테이블 형식 함수와 같은 T-sql에서 Kusto 저장 함수를 호출할 수도 있습니다.

```sql
SELECT * FROM kusto.MyFunction(10)
```

최적화 된 KQL 쿼리를 만들고 저장 된 함수에 캡슐화 하 여 T-sql 쿼리 코드를 최소화 합니다.

## <a name="executing-kql-query"></a>KQL 쿼리 실행

저장 프로시저 `sp_execute_kql` 는 [KQL](../../query/index.md) 쿼리 (매개 변수가 있는 쿼리 포함)를 실행 합니다. 이 절차는 SQL server `sp_executesql`와 유사 합니다.

의 `sp_execute_kql` 첫 번째 매개 변수는 KQL 쿼리입니다. 추가 매개 변수를 도입할 수 있으며, [쿼리 매개 변수](../../query/queryparametersstatement.md)처럼 작동 합니다.

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

> [! 참고:] 매개 변수 형식은 프로토콜을 통해 설정 되므로 TDS를 통해 호출할 때 매개 변수를 선언할 필요가 없습니다.
