---
title: 데이터베이스 간 및 클러스터 간 쿼리-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기에서 데이터베이스 간 및 클러스터 간 쿼리를 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: eaf42247840bfc5446c61bcefbb205c9e49706c3
ms.sourcegitcommit: d885c0204212dd83ec73f45fad6184f580af6b7e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82737745"
---
# <a name="cross-database-and-cross-cluster-queries"></a>데이터베이스 간/클러스터 간 쿼리

::: zone pivot="azuredataexplorer"

모든 Kusto 쿼리는 현재 클러스터와 기본 데이터베이스의 컨텍스트에서 작동 합니다.
* [Kusto 탐색기](../tools/kusto-explorer.md) 에서 기본 데이터베이스는 [연결 패널](../tools/kusto-explorer.md#connections-panel) 에서 선택 하 고 현재 클러스터는 해당 데이터베이스를 포함 하는 연결입니다.
* [Kusto 클라이언트 라이브러리](../api/netfx/about-kusto-data.md) 를 사용 하는 경우 현재 클러스터와 기본 데이터베이스는 각각 [kusto 연결 문자열](../api/connection-strings/kusto.md) 의 `Data Source` 및 `Initial Catalog` 속성으로 지정 됩니다.

## <a name="queries"></a>쿼리
기본값이 아닌 다른 데이터베이스의 테이블에 액세스 하려면 *정규화 된 이름* 구문을 사용 해야 합니다. 현재 클러스터의 데이터베이스에 액세스 하려면
```kusto
database("<database name>").<table name>
```
원격 클러스터의 데이터베이스:
```kusto
cluster("<cluster name>").database("<database name>").<table name>
```

*데이터베이스 이름은* 대/소문자를 구분 합니다.

*클러스터 이름은* 대/소문자를 구분 하지 않으며 다음 형식 중 하나일 수 있습니다.
* 올바른 형식의 URL: 예 `http://contoso.kusto.windows.net:1234/`, HTTP 및 HTTPS 스키마만 지원 됩니다.
* FQDN (정규화 된 도메인 이름): 예를 `contoso.kusto.windows.net` 들면와 동일 합니다.`https://`**`contoso.kusto.windows.net`**`:443/`
* 약식 이름 (도메인 파트가 없는 호스트 이름 [및 지역]): `contoso` 예 `https://` **`contoso`** `.kusto.windows.net:443/`를 들어로 해석 되는 또는 `contoso.westus` 입니다.`https://`**`contoso.westus`**`.kusto.windows.net:443/`

> [!NOTE]
> 데이터베이스 간 액세스에는 일반적인 권한 검사가 적용 됩니다.
> 쿼리를 excute 하려면 기본 데이터베이스와 쿼리에서 참조 하는 다른 모든 데이터베이스 (현재 및 원격 클러스터)에 대 한 읽기 권한이 있어야 합니다.

*정규화 된 이름은* 테이블 이름을 사용할 수 있는 모든 컨텍스트에서 사용할 수 있습니다.
다음은 모두 유효 합니다.

```kusto
database("OtherDb").Table | where ...

union Table1, cluster("OtherCluster").database("OtherDb").Table2 | project ...

database("OtherDb1").Table1 | join cluster("OtherCluster").database("OtherDb2").Table2 on Key | join Table3 on Key | extend ...
```

*정규화 된 이름이* [union 연산자](./unionoperator.md)의 피연산자로 표시 되 면 와일드 카드를 사용 하 여 여러 테이블 및 여러 데이터베이스를 지정할 수 있습니다. 클러스터 이름에는 와일드 카드를 사용할 수 없습니다.

```kusto
union withsource=TableName *, database("OtherDb*").*Table, cluster("OtherCluster").database("*").*
```

> [!NOTE]
>* 기본 데이터베이스의 이름도 잠재적으로 일치 하므로 데이터베이스 ("&#42;"). *는 기본값을 포함 하 여 모든 데이터베이스의 모든 테이블을 지정 합니다.
>* 스키마 변경에 대 한 자세한 내용은 클러스터 간 쿼리 [및 스키마 변경 내용](../concepts/crossclusterandschemachanges.md) 을 참조 하세요.

## <a name="access-restriction"></a>액세스 제한 
정규화 된 이름 또는 패턴을 [제한 액세스](./restrictstatement.md) 문에 포함할 수도 있습니다 (클러스터 이름의 와일드 카드는 사용할 수 없음).
```kusto
restrict access to (my*, database("MyOther*").*, cluster("OtherCluster").database("my2*").*);
```

위의 작업을 수행 하면 다음 entites에 대 한 쿼리 액세스가 제한 됩니다.

* 기본 데이터베이스에서 *my ...* 로 시작 하는 모든 엔터티 이름입니다. 
* 현재 클러스터의 *Myother ...* 라는 모든 데이터베이스의 모든 테이블
* 클러스터 *OtherCluster.kusto.windows.net*의 *my2* 이라는 모든 데이터베이스에 있는 모든 테이블

## <a name="functions-and-views"></a>함수 및 뷰

함수와 뷰 (persistent 및 created inline)는 데이터베이스 및 클러스터 경계를 넘어 테이블을 참조 수 있습니다. 유효한는 다음과 같습니다.

```kusto
let MyView = Table1 join database("OtherDb").Table2 on Key | join cluster("OtherCluster").database("SomeDb").Table3 on Key;
MyView | where ...
```

영구 함수와 보기는 동일한 클러스터의 다른 데이터베이스에서 액세스할 수 있습니다.

테이블 형식 함수 (뷰) `OtherDb`:

```kusto
.create function MyView(v:string) { Table1 | where Column1 has v ...  }  
```

스칼라 함수 `OtherDb`:
```kusto
.create function MyCalc(a:double, b:double, c:double) { (a + b) / c }  
```

기본 데이터베이스:

```kusto
database("OtherDb").MyView("exception") | extend CalCol=database("OtherDb").MyCalc(Col1, Col2, Col3) | limit 10
```

## <a name="limitations-of-cross-cluster-function-calls"></a>클러스터 간 함수 호출의 제한 사항

테이블 형식 함수 또는 뷰를 클러스터에서 참조할 수 있습니다. 다음과 같은 제한 사항이 적용 됩니다.

1. 원격 함수는 테이블 형식 스키마를 반환 해야 합니다. 스칼라 함수는 동일한 클러스터 에서만 액세스할 수 있습니다.
2. 원격 함수는 스칼라 매개 변수만 사용할 수 있습니다. 하나 이상의 테이블 인수를 가져오는 함수는 동일한 클러스터 에서만 액세스할 수 있습니다.
3. 원격 함수의 스키마는 해당 매개 변수의 알려진 및 고정 이어야 합니다 ( [클러스터 간 쿼리 및 스키마 변경](../concepts/crossclusterandschemachanges.md)도 참조).

다음 클러스터 간 호출은 **유효**합니다.

```kusto
cluster("OtherCluster").database("SomeDb").MyView("exception") | count
```

다음 쿼리는 원격 스칼라 함수 `MyCalc`를 호출 합니다.
이는 규칙 #1 위반 하므로 **유효 하지**않습니다.

```kusto
MyTable | extend CalCol=cluster("OtherCluster").database("OtherDb").MyCalc(Col1, Col2, Col3) | limit 10
```

다음 쿼리는 원격 함수 `MyCalc` 를 호출 하 고 테이블 형식 매개 변수를 제공 합니다.
이는 규칙 #2 위반 하므로 **유효 하지**않습니다.

```kusto
cluster("OtherCluster").database("OtherDb").MyCalc(datatable(x:string, y:string)["x","y"] ) 
```

다음 쿼리는 매개 변수 `SomeTable` `tablename`를 기반으로 하는 변수 스키마 출력이 있는 원격 함수를 호출 합니다.
이는 규칙 #3 위반 하므로 **유효 하지**않습니다.

테이블 형식 함수 `OtherDb`:
```kusto
.create function SomeTable(tablename:string) { table(tablename)  }  
```

기본 데이터베이스:
```kusto
cluster("OtherCluster").database("OtherDb").SomeTable("MyTable")
```

다음 쿼리는 데이터를 기반 `GetDataPivot` 으로 하는 변수 스키마 출력을 포함 하는 원격 함수를 호출 합니다 ([pivot () 플러그 인](pivotplugin.md) 은 동적 출력).
이는 규칙 #3 위반 하므로 **유효 하지**않습니다.

테이블 형식 함수 `OtherDb`:
```kusto
.create function GetDataPivot() { T | evaluate pivot(PivotColumn) }  
```

기본 데이터베이스의 테이블 형식 함수:
```kusto
cluster("OtherCluster").database("OtherDb").GetDataPivot()
```

## <a name="displaying-data"></a>데이터 표시

클라이언트에 데이터를 반환 하는 문은 `take` 연산자를 특정 하 게 사용 하지 않아도 반환 되는 레코드 수에 의해 암시적으로 제한 됩니다. 이 제한을 올리려면 `notruncation` 클라이언트 요청 옵션을 사용합니다.

데이터를 그래픽 형식으로 표시 하려면 [render 연산자](renderoperator.md)를 사용 합니다.

::: zone-end

::: zone pivot="azuremonitor"

이 기능은에서 지원 되지 않습니다 Azure Monitor

::: zone-end
