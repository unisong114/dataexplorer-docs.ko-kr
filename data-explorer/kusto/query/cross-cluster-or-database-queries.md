---
title: 데이터베이스 간 및 클러스터 간 쿼리 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 교차 데이터베이스 및 클러스터 간 쿼리에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 8bb0cf2674bae801fb98d14d93518f5617af6807
ms.sourcegitcommit: 01eb9aaf1df2ebd5002eb7ea7367a9ef85dc4f5d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81766070"
---
# <a name="cross-database-and-cross-cluster-queries"></a>데이터베이스 간/클러스터 간 쿼리

::: zone pivot="azuredataexplorer"

모든 Kusto 쿼리는 현재 클러스터와 기본 데이터베이스의 컨텍스트에서 작동합니다.
* [Kusto Explorer에서](../tools/kusto-explorer.md) 기본 데이터베이스는 연결 [패널에서](../tools/kusto-explorer.md#connections-panel) 선택한 데이터베이스이고 현재 클러스터는 해당 데이터베이스를 포함하는 연결입니다.
* [Kusto 클라이언트 라이브러리를](../api/netfx/about-kusto-data.md) 사용하는 경우 현재 클러스터와 `Data Source` 기본 `Initial Catalog` 데이터베이스는 [Kusto 연결 문자열의](../api/connection-strings/kusto.md) 및 속성에 의해 각각 지정됩니다.

## <a name="queries"></a>쿼리
기본값이 아닌 다른 데이터베이스에서 테이블에 액세스하려면 현재 클러스터의 데이터베이스에 액세스하려면 *정규화된 이름* 구문을 사용해야 합니다.
```kusto
database("<database name>").<table name>
```
원격 클러스터의 데이터베이스:
```kusto
cluster("<cluster name>").database("<database name>").<table name>
```

*데이터베이스 이름은* 대/소문자를 구분합니다.

*클러스터 이름은* 대/소문자를 구분하지 않으며 다음 양식 중 하나일 수 있습니다.
* 잘 형성된 URL: 예를 들어 `http://contoso.kusto.windows.net:1234/`HTTP 및 HTTPS 스키마만 지원됩니다.
* 정규화된 도메인 이름(FQDN): `contoso.kusto.windows.net` 예를 들어`https://`**`contoso.kusto.windows.net`**`:443/`
* 짧은 이름(도메인 부분이 없는 호스트 이름 [및 `contoso` 지역]): `https://` **`contoso`** `.kusto.windows.net:443/`예를 `contoso.westus` 들어 로 해석되거나`https://`**`contoso.westus`**`.kusto.windows.net:443/`

> [!NOTE]
> 데이터베이스 간 액세스는 일반적인 권한 검사의 적용을 받습니다.
> 쿼리를 제거하려면 기본 데이터베이스및 쿼리에서 참조되는 다른 모든 데이터베이스(현재 및 원격 클러스터)에 대한 읽기 권한이 있어야 합니다.

*정규화된 이름은* 테이블 이름을 사용할 수 있는 모든 컨텍스트에서 사용할 수 있습니다.
다음 의 모든 유효합니다.

```kusto
database("OtherDb").Table | where ...

union Table1, cluster("OtherCluster").database("OtherDb").Table2 | project ...

database("OtherDb1").Table1 | join cluster("OtherCluster").database("OtherDb2").Table2 on Key | join Table3 on Key | extend ...
```

*정규화된 이름이* [공용 구조체 연산자의](./unionoperator.md)발판으로 나타나면 와일드카드를 사용하여 여러 테이블과 여러 데이터베이스를 지정할 수 있습니다. 와일드카드는 클러스터 이름에 허용되지 않습니다.

```kusto
union withsource=TableName *, database("OtherDb*").*Table, cluster("OtherCluster").database("*").*
```

> [!NOTE]
>* 기본 데이터베이스의 이름도 잠재적 일치하므로 데이터베이스("&#42;")*는 기본값을 포함한 모든 데이터베이스의 모든 테이블을 지정합니다.
>* 스키마 변경 사항이 클러스터 간 쿼리에 미치는 영향, [클러스터 간 쿼리 및 스키마 변경](../concepts/crossclusterandschemachanges.md) 사항 참조

## <a name="access-restriction"></a>액세스 제한 
권한 있는 이름 또는 패턴을 [제한 액세스](./restrictstatement.md) 문에 포함할 수도 있습니다(클러스터 이름의 와일드카드는 허용되지 않음).
```kusto
restrict access to (my*, database("MyOther*").*, cluster("OtherCluster").database("my2*").*);
```

위의 내용은 다음 엔티티에 대한 쿼리 액세스를 제한합니다.

* 기본 데이터베이스에서 *내* 엔터티 이름으로 시작합니다. 
* 현재 클러스터의 *MyOther...*
* *클러스터OtherCluster.kusto.windows.net* *my2라는* 모든 데이터베이스의 테이블

## <a name="functions-and-views"></a>함수 및 뷰

함수 및 뷰(영구 및 생성된 인라인)는 데이터베이스 및 클러스터 경계에서 테이블을 참조할 수 있습니다. 다음은 유효합니다.

```kusto
let MyView = Table1 join database("OtherDb").Table2 on Key | join cluster("OtherCluster").database("SomeDb").Table3 on Key;
MyView | where ...
```

영구 함수 및 뷰는 동일한 클러스터의 다른 데이터베이스에서 액세스할 수 있습니다.

표 형식 함수(보기)에서 `OtherDb`:

```kusto
.create function MyView(v:string) { Table1 | where Column1 has v ...  }  
```

스칼라 기능 : `OtherDb`
```kusto
.create function MyCalc(a:double, b:double, c:double) { (a + b) / c }  
```

기본 데이터베이스에서:

```kusto
database("OtherDb").MyView("exception") | extend CalCol=database("OtherDb").MyCalc(Col1, Col2, Col3) | limit 10
```

## <a name="limitations-of-cross-cluster-function-calls"></a>클러스터 간 기능 호출의 제한 사항

테이블 형식 함수 또는 보기는 클러스터 간에 참조할 수 있습니다. 다음과 같은 제한 사항이 적용됩니다.

1. 원격 함수는 테이블 형식 스키마를 반환해야 합니다. 스칼라 함수는 동일한 클러스터에서만 액세스할 수 있습니다.
2. 원격 함수는 스칼라 매개변수만 허용할 수 있습니다. 하나 이상의 테이블 인수를 얻는 함수는 동일한 클러스터에서만 액세스할 수 있습니다.
3. 원격 함수의 스키마는 해당 매개 변수를 알고 고정해야 [합니다(클러스터 간 쿼리 및 스키마 변경](../concepts/crossclusterandschemachanges.md)사항 참조).

다음 클러스터 간 호출은 **유효합니다.**

```kusto
cluster("OtherCluster").database("SomeDb").MyView("exception") | count
```

다음 쿼리는 원격 스칼라 `MyCalc`함수를 호출합니다.
이는 규칙을 위반하는 #1 때문에 **유효하지 않습니다.**

```kusto
MyTable | extend CalCol=cluster("OtherCluster").database("OtherDb").MyCalc(Col1, Col2, Col3) | limit 10
```

다음 쿼리는 원격 `MyCalc` 함수를 호출하고 테이블 매개 변수를 제공합니다.
이것은 규칙을 위반하는 #2 때문에 **유효하지 않습니다.**

```kusto
cluster("OtherCluster").database("OtherDb").MyCalc(datatable(x:string, y:string)["x","y"] ) 
```

다음 쿼리는 매개 `SomeTable` 변수를 `tablename`기반으로 가변 스키마 출력이 있는 원격 함수를 호출합니다.
이것은 규칙을 위반하는 #3 때문에 **유효하지 않습니다.**

표 형식 `OtherDb`함수 :
```kusto
.create function SomeTable(tablename:string) { table(tablename)  }  
```

기본 데이터베이스에서:
```kusto
cluster("OtherCluster").database("OtherDb").SomeTable("MyTable")
```

다음 쿼리는 데이터(pivot() `GetDataPivot` [플러그인에](pivotplugin.md) 기반한 가변 스키마 출력이 있는 원격 함수를 호출합니다.
이것은 규칙을 위반하는 #3 때문에 **유효하지 않습니다.**

표 형식 `OtherDb`함수 :
```kusto
.create function GetDataPivot() { T | evaluate pivot(PivotColumn) }  
```

기본 데이터베이스의 테이블 형식 함수:
```kusto
cluster("OtherCluster").database("OtherDb").GetDataPivot()
```

## <a name="displaying-data"></a>데이터 표시

`take` 클라이언트에 데이터를 반환하는 문은 연산자의 특별한 사용이 없는 경우에도 반환되는 레코드 수에 따라 암시적으로 제한됩니다. 이 제한을 올리려면 `notruncation` 클라이언트 요청 옵션을 사용합니다.

그래픽 형식으로 데이터를 표시하려면 [렌더 연산자](renderoperator.md)를 사용합니다.

::: zone-end

::: zone pivot="azuremonitor"

Azure 모니터에서는 이 성능이 지원되지 않습니다.

::: zone-end
