---
title: 클러스터() (범위 함수) - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 클러스터(범위 함수)에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: c5f537b47006af4035c9db26388c1d4110c69b55
ms.sourcegitcommit: 01eb9aaf1df2ebd5002eb7ea7367a9ef85dc4f5d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81766109"
---
# <a name="cluster-scope-function"></a>클러스터() (범위 함수)

::: zone pivot="azuredataexplorer"

쿼리 참조를 원격 클러스터로 변경합니다. 

```kusto
cluster('help').database('Sample').SomeTable
```

**구문**

`cluster(`*문자열 불변*`)`

**인수**

* *stringConstant*: 참조되는 클러스터의 이름입니다. 클러스터 이름은 정규화된 DNS 이름또는 `.kusto.windows.net`에 접미사가 붙일 문자열일 수 있습니다. 인수는 쿼리를 실행하기 전에 _일정해야_ 합니다.

**참고 사항**

* 동일한 클러스터 내에서 데이터베이스에 액세스하는 경우 [- 데이터베이스()](databasefunction.md) 함수를 사용합니다.
* 클러스터 간 쿼리 및 데이터베이스 간 쿼리에 대한 자세한 내용은 [여기에서](cross-cluster-or-database-queries.md) 확인할 수 있습니다.  

## <a name="examples"></a>예

### <a name="use-cluster-to-access-remote-cluster"></a>클러스터()를 사용하여 원격 클러스터에 액세스 

다음 쿼리는 Kusto 클러스터에서 실행할 수 있습니다.

```kusto
cluster('help').database('Samples').StormEvents | count

cluster('help.kusto.windows.net').database('Samples').StormEvents | count  
```

|개수|
|---|
|59066|

### <a name="use-cluster-inside-let-statements"></a>내부 let 문 에서 클러스터() 사용 

클러스터() 함수로 전달되는 매개 변수를 `clusterName` 수신하는 인라인 함수(let statement)를 사용하기 위해 위와 동일한 쿼리를 다시 작성할 수 있습니다.

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

### <a name="use-cluster-inside-functions"></a>함수 내부의 클러스터() 사용 

클러스터() 함수로 전달되는 매개 변수를 `clusterName` 수신하는 함수에서 위와 동일한 쿼리를 다시 작성할 수 있습니다.

```kusto
.create function foo(clusterName:string)
{
    cluster(clusterName).database('Samples').StormEvents | count
};
```

**참고:** 이러한 함수는 클러스터 간 쿼리가 아닌 로컬에서만 사용할 수 있습니다.

::: zone-end

::: zone pivot="azuremonitor"

Azure 모니터에서는 이 성능이 지원되지 않습니다.

::: zone-end
