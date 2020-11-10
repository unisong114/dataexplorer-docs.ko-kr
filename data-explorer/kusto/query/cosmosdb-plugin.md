---
title: cosmosdb_sql_request 플러그 인-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 cosmosdb_sql_request 플러그 인을 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: miwalia
ms.service: data-explorer
ms.topic: reference
ms.date: 09/11/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 7c95f7676ecdc88deefeae5db3f904dd5048a5ba
ms.sourcegitcommit: 25c0440cb0390b9629b819611844f1375de00a66
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94417594"
---
# <a name="cosmosdb_sql_request-plugin"></a>cosmosdb_sql_request 플러그인

::: zone pivot="azuredataexplorer"

`cosmosdb_sql_request`플러그 인이 sql 쿼리를 COSMOS DB sql 네트워크 끝점으로 전송 하 고 쿼리 결과를 반환 합니다. 이 플러그 인은 주로 작은 데이터 집합을 쿼리 하는 데 사용 됩니다. 예를 들어 [Azure Cosmos DB](/azure/cosmos-db/)에 저장 된 참조 데이터를 사용 하 여 데이터를 보강

## <a name="syntax"></a>구문

`evaluate``cosmosdb_sql_request` `(` *ConnectionString* `,` *SqlQuery* [ `,` *sqlparameters* [ `,` *Options* ]]`)`

## <a name="arguments"></a>인수

|인수 이름 | Description | 필수/선택 | 
|---|---|---|
| *ConnectionString* | `string`쿼리할 Cosmos DB 컬렉션을 가리키는 연결 문자열을 나타내는 리터럴입니다. *Accountendpoint* , *데이터베이스* 및 *컬렉션* 을 포함 해야 합니다. 인증에 마스터 키를 사용 하는 경우 *AccountKey* 이 포함 될 수 있습니다. <br> **예제:** `'AccountEndpoint=https://cosmosdbacc.documents.azure.com:443/ ;Database=MyDatabase;Collection=MyCollection;AccountKey=' h'R8PM...;'`| 필수 |
| *SqlQuery*| `string`실행할 쿼리를 나타내는 리터럴입니다. | 필수 |
| *Sqlparameter* | `dynamic`쿼리와 함께 매개 변수로 전달할 키-값 쌍을 보유 하는 형식의 상수 값입니다. 매개 변수 이름은로 시작 해야 합니다 `@` . | 선택 사항 |
| *Options* | `dynamic`키-값 쌍으로 고급 설정을 포함 하는 형식의 상수 값입니다. | 선택 사항 |
|| ----*지원 되는 옵션 설정은 다음과 같습니다.*-----
|      `armResourceId` | Azure Resource Manager에서 API 키를 검색 합니다. <br> **예제:** `/subscriptions/a0cd6542-7eaf-43d2-bbdd-b678a869aad1/resourceGroups/ cosmoddbresourcegrouput/providers/Microsoft.DocumentDb/databaseAccounts/cosmosdbacc`| 
|  `token` | Azure Resource Manager를 사용 하 여 인증 하는 데 사용 되는 Azure AD 액세스 토큰을 제공 합니다.
| `preferredLocations` | 데이터를 쿼리 하는 영역을 제어 합니다. <br> **예제:** `['East US']` | |  

## <a name="set-callout-policy"></a>콜아웃 정책 설정

플러그 인이 Cosmos DB에 대 한 설명선을 만듭니다. 클러스터의 [콜아웃 정책이](../management/calloutpolicy.md) 대상 CosmosDbUri에 대 한 형식의 호출을 활성화 하는지 확인 `cosmosdb` 합니다. *CosmosDbUri*

다음 예제에서는 Cosmos DB에 대 한 콜아웃 정책을 정의 하는 방법을 보여 줍니다. 특정 끝점 (,)으로 제한 하는 것이 좋습니다 `my_endpoint1` `my_endpoint2` .

```kusto
[
  {
    "CalloutType": "CosmosDB",
    "CalloutUriRegex": "my_endpoint1\\.documents\\.azure\\.com",
    "CanCall": true
  },
  {
    "CalloutType": "CosmosDB",
    "CalloutUriRegex": "my_endpoint2\\.documents\\.azure\\.com",
    "CanCall": true
  }
]
```

다음 예에서는 `cosmosdb` *callouttype* 에 대 한 alter 콜아웃 정책 명령을 보여 줍니다.

```kusto
.alter cluster policy callout @'[{"CalloutType": "cosmosdb", "CalloutUriRegex": "\\.documents\\.azure\\.com", "CanCall": true}]'
```

## <a name="examples"></a>예제

### <a name="query-cosmos-db"></a>쿼리 Cosmos DB

다음 예에서는 *cosmosdb_sql_request* 플러그 인을 사용 하 여 sql API를 통해 Cosmos DB에서 데이터를 가져오는 sql 쿼리를 보냅니다.

```kusto
evaluate cosmosdb_sql_request(
  'AccountEndpoint=https://cosmosdbacc.documents.azure.com:443/;Database=MyDatabase;Collection=MyCollection;AccountKey=' h'R8PM...;',
  'SELECT * from c')
```

### <a name="query-cosmos-db-with-parameters"></a>매개 변수를 사용 하 여 쿼리 Cosmos DB

다음 예에서는 SQL 쿼리 매개 변수를 사용 하 고 대체 지역에서 데이터를 쿼리 합니다. 자세한 내용은 [`preferredLocations`](/azure/cosmos-db/tutorial-global-distribution-sql-api?tabs=dotnetv2%2Capi-async#preferred-locations)을(를) 참조하세요.

```kusto
evaluate cosmosdb_sql_request(
    'AccountEndpoint=https://cosmosdbacc.documents.azure.com:443/;Database=MyDatabase;Collection=MyCollection;AccountKey=' h'R8PM...;',
    "SELECT c.id, c.lastName, @param0 as Column0 FROM c WHERE c.dob >= '1970-01-01T00:00:00Z'",
    dynamic({'@param0': datetime(2019-04-16 16:47:26.7423305)}),
    dynamic({'preferredLocations': ['East US']}))
| where lastName == 'Smith'
```

::: zone-end

::: zone pivot="azuremonitor"

이 기능은에서 지원 되지 않습니다 Azure Monitor

::: zone-end
