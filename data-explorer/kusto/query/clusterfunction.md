---
title: cluster () (scope 함수)-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 cluster () (범위 함수)에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: b2f9dd3fd3eede1f6d527c97b905353f9a331620
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92253068"
---
# <a name="cluster-scope-function"></a>cluster () (범위 함수)

::: zone pivot="azuredataexplorer"

쿼리 참조를 원격 클러스터로 변경 합니다. 

```kusto
cluster('help').database('Sample').SomeTable
```

## <a name="syntax"></a>구문

`cluster(`*stringConstant*`)`

## <a name="arguments"></a>인수

* *Stringconstant*: 참조 된 클러스터의 이름입니다. 클러스터 이름은 정규화 된 DNS 이름 이거나 접미사로 사용 되는 문자열일 수 있습니다 `.kusto.windows.net` . 쿼리를 실행 하기 전에 인수를 _일정_ 해야 합니다. 즉, 하위 쿼리 계산에서 가져올 수 없습니다.

**참고**

* 동일한 클러스터 내에서 데이터베이스에 액세스 하는 경우- [database ()](databasefunction.md) 함수를 사용 합니다.
* 클러스터 간 및 데이터베이스 간 쿼리에 대 한 자세한 내용은 여기를 참조 [하세요](cross-cluster-or-database-queries.md) .  

## <a name="examples"></a>예

### <a name="use-cluster-to-access-remote-cluster"></a>클러스터 ()를 사용 하 여 원격 클러스터에 액세스 

다음 쿼리는 Kusto 클러스터에서 실행할 수 있습니다.

```kusto
cluster('help').database('Samples').StormEvents | count

cluster('help.kusto.windows.net').database('Samples').StormEvents | count  
```

|개수|
|---|
|59066|

### <a name="use-cluster-inside-let-statements"></a>Let 문 내에서 cluster () 사용 

위와 동일한 쿼리는 `clusterName` cluster () 함수로 전달 되는 매개 변수를 받는 인라인 함수 (let 문)를 사용 하도록 다시 작성할 수 있습니다.

```kusto
let foo = (clusterName:string)
{
    cluster(clusterName).database('Samples').StormEvents | count
};
foo('help')
```

|개수|
|---|
|59066|

### <a name="use-cluster-inside-functions"></a>함수 내에서 cluster () 사용 

위와 동일한 쿼리는 `clusterName` cluster () 함수로 전달 되는 매개 변수를 받는 함수에서 사용 하도록 다시 작성할 수 있습니다.

```kusto
.create function foo(clusterName:string)
{
    cluster(clusterName).database('Samples').StormEvents | count
};
```

**참고:** 이러한 함수는 로컬 에서만 사용할 수 있고 클러스터 간 쿼리에서는 사용할 수 없습니다.

::: zone-end

::: zone pivot="azuremonitor"

이 기능은에서 지원 되지 않습니다 Azure Monitor

::: zone-end
