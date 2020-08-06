---
title: database () (범위 함수)-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 데이터베이스 () (범위 함수)에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 6511006373cd1f6245a0dcc04537f3994183d63e
ms.sourcegitcommit: 3dfaaa5567f8a5598702d52e4aa787d4249824d4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/05/2020
ms.locfileid: "87803763"
---
# <a name="database-scope-function"></a>database () (범위 함수)

::: zone pivot="azuredataexplorer"

쿼리 참조를 클러스터 범위 내의 특정 데이터베이스로 변경 합니다. 

```kusto
database('Sample').StormEvents
cluster('help').database('Sample').StormEvents
```

> [!NOTE]
> * 자세한 내용은 [데이터베이스 간 및 클러스터 간 쿼리](cross-cluster-or-database-queries.md)를 참조 하세요.
> * 원격 클러스터 및 원격 데이터베이스에 액세스 하려면 [cluster ()](clusterfunction.md) 범위 함수를 참조 하세요.

## <a name="syntax"></a>구문

`database(`*stringConstant*`)`

## <a name="arguments"></a>인수

* *Stringconstant*: 참조 되는 데이터베이스의 이름입니다. 확인 된 데이터베이스는 또는 중 하나일 수 있습니다 `DatabaseName` `PrettyName` . 인수는 쿼리를 실행 하기 전에 반드시 _일정_ 해야 합니다. 즉, 하위 쿼리 계산에서 가져올 수 없습니다.

## <a name="examples"></a>예

### <a name="use-database-to-access-table-of-other-database"></a>데이터베이스 ()를 사용 하 여 다른 데이터베이스의 테이블에 액세스

```kusto
database('Samples').StormEvents | count
```

|개수|
|---|
|59066|

### <a name="use-database-inside-let-statements"></a>Let 문 내에서 database () 사용 

위와 동일한 쿼리를 `dbName` 데이터베이스 () 함수로 전달 되는 매개 변수를 받는 인라인 함수 (let 문)를 사용 하도록 다시 작성할 수 있습니다.

```kusto
let foo = (dbName:string)
{
    database(dbName).StormEvents | count
};
foo('help')
```

|개수|
|---|
|59066|

### <a name="use-database-inside-functions"></a>함수 내에서 database () 사용 

위와 동일한 쿼리를 `dbName` 데이터베이스 () 함수로 전달 되는 매개 변수를 받는 함수에서 사용 하도록 다시 작성할 수 있습니다.

```kusto
.create function foo(dbName:string)
{
    database(dbName).StormEvents | count
};
```

> [!NOTE]
> 이러한 함수는 로컬 에서만 사용할 수 있으며 클러스터 간 쿼리에서는 사용할 수 없습니다.

::: zone-end

::: zone pivot="azuremonitor"

이 기능은에서 지원 되지 않습니다 Azure Monitor

::: zone-end
