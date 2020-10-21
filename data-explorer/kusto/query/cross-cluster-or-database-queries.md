---
title: 크로스 데이터베이스 & 클러스터 간 쿼리-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 데이터베이스 간 및 클러스터 간 쿼리를 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 57b7b6b4c67e0e8903903cef670a561b30b3904e
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252572"
---
# <a name="cross-database-and-cross-cluster-queries"></a>데이터베이스 간/클러스터 간 쿼리

::: zone pivot="azuredataexplorer"

모든 Kusto 쿼리는 현재 클러스터의 컨텍스트와 기본 데이터베이스에서 작동 합니다.
* [Kusto 탐색기](../tools/kusto-explorer.md)에서 기본 데이터베이스는 [연결 패널](../tools/kusto-explorer.md#connections-panel)에서 선택 된 데이터베이스이 고 현재 클러스터는 해당 데이터베이스를 포함 하는 연결입니다.
* [클라이언트 라이브러리](../api/netfx/about-kusto-data.md)를 사용 하는 경우 현재 클러스터와 기본 데이터베이스는 `Data Source` `Initial Catalog` 각각 [연결 문자열](../api/connection-strings/kusto.md)의 및 속성으로 지정 됩니다.

## <a name="queries"></a>쿼리
기본값이 아닌 다른 데이터베이스의 테이블에 액세스 하려면 *정규화 된 이름* 구문을 사용 해야 합니다.

현재 클러스터의 데이터베이스에 액세스 합니다.

```kusto
database("<database name>").<table name>
```

원격 클러스터의 데이터베이스입니다.
```kusto
cluster("<cluster name>").database("<database name>").<table name>
```

*데이터베이스 이름은* 대/소문자를 구분 합니다.

*클러스터 이름은* 대/소문자를 구분 하지 않으며 다음 형식 중 하나일 수 있습니다.
   * 과 같은 올바른 형식의 URL `http://contoso.kusto.windows.net:1234/` 입니다. HTTP 및 HTTPS 스키마만 지원 됩니다.
   * FQDN (정규화 된 도메인 이름) (예:) `contoso.kusto.windows.net` . 이 문자열은와 동일 `https://` **`contoso.kusto.windows.net`** `:443/` 합니다.
   * 약식 이름 (도메인 파트가 없는 호스트 이름 [및 지역]) (예: `contoso` 또는) `contoso.westus` . 이러한 문자열은 및로 해석 됩니다 `https://` **`contoso`** `.kusto.windows.net:443/` `https://` **`contoso.westus`** `.kusto.windows.net:443/` .

> [!NOTE]
> 데이터베이스 간 액세스에는 일반적인 권한 검사가 적용 됩니다.
> 쿼리를 실행 하려면 기본 데이터베이스와 쿼리에서 참조 하는 다른 모든 데이터베이스 (현재 및 원격 클러스터)에 대 한 읽기 권한이 있어야 합니다.

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
> * 기본 데이터베이스의 이름도 잠재적으로 일치 하므로 데이터베이스 ("&#42;")는 기본값을 포함 하 여 모든 데이터베이스의 모든 테이블을 지정 합니다.
> * 스키마 변경이 클러스터 간 쿼리에 영향을 주는 방법에 대 한 자세한 정보는 [클러스터 간 쿼리 및 스키마 변경 내용](../concepts/crossclusterandschemachanges.md) 을 참조 하세요.

## <a name="access-restriction"></a>액세스 제한

정규화 된 이름 또는 패턴을 [제한 액세스](./restrictstatement.md) 문에 포함할 수도 있습니다. 클러스터 이름의 와일드 카드는 허용 되지 않습니다.

```kusto
restrict access to (my*, database("MyOther*").*, cluster("OtherCluster").database("my2*").*);
```

위의에서는 다음 엔터티에 대 한 쿼리 액세스를 제한 합니다.

* 기본 데이터베이스에서 *my ...* 로 시작 하는 모든 엔터티 이름입니다. 
* 현재 클러스터의 *Myother ...* 라는 모든 데이터베이스의 모든 테이블
* 클러스터 *OtherCluster.kusto.windows.net*의 *my2* 이라는 모든 데이터베이스에 있는 모든 테이블

## <a name="functions-and-views"></a>함수 및 뷰

함수와 뷰 (persistent 및 created inline)는 데이터베이스 및 클러스터 경계에서 테이블을 참조할 수 있습니다. 다음 코드는 유효 합니다.

```kusto
let MyView = Table1 join database("OtherDb").Table2 on Key | join cluster("OtherCluster").database("SomeDb").Table3 on Key;
MyView | where ...
```

영구 함수와 보기는 동일한 클러스터의 다른 데이터베이스에서 액세스할 수 있습니다.

의 테이블 형식 함수 (뷰) `OtherDb` 입니다.

```kusto
.create function MyView(v:string) { Table1 | where Column1 has v ...  }  
```

의 스칼라 함수 `OtherDb` 입니다.

```kusto
.create function MyCalc(a:double, b:double, c:double) { (a + b) / c }  
```

기본 데이터베이스에 있습니다.

```kusto
database("OtherDb").MyView("exception") | extend CalCol=database("OtherDb").MyCalc(Col1, Col2, Col3) | limit 10
```

## <a name="limitations-of-cross-cluster-function-calls"></a>클러스터 간 함수 호출의 제한 사항

테이블 형식 함수 또는 뷰를 클러스터에서 참조할 수 있습니다. 다음과 같은 제한 사항이 적용됩니다.

* 원격 함수는 테이블 형식 스키마를 반환 해야 합니다. 스칼라 함수는 동일한 클러스터 에서만 액세스할 수 있습니다.
* 원격 함수는 스칼라 매개 변수만 사용할 수 있습니다. 하나 이상의 테이블 인수를 가져오는 함수는 동일한 클러스터 에서만 액세스할 수 있습니다.
* 원격 함수의 스키마는 해당 매개 변수의 알려진 및 고정 이어야 합니다. 자세한 내용은 [클러스터 간 쿼리 및 스키마 변경](../concepts/crossclusterandschemachanges.md)을 참조 하세요.

다음 클러스터 간 호출이 유효 합니다.

```kusto
cluster("OtherCluster").database("SomeDb").MyView("exception") | count
```

다음 쿼리는 원격 스칼라 함수를 호출 합니다 `MyCalc` .
이 호출은 규칙 #1를 위반 하므로 유효 하지 않습니다.

```kusto
MyTable | extend CalCol=cluster("OtherCluster").database("OtherDb").MyCalc(Col1, Col2, Col3) | limit 10
```

다음 쿼리는 원격 함수를 호출 `MyCalc` 하 고 테이블 형식 매개 변수를 제공 합니다.
이 호출은 규칙 #2를 위반 하므로 유효 하지 않습니다.

```kusto
cluster("OtherCluster").database("OtherDb").MyCalc(datatable(x:string, y:string)["x","y"] )
```

다음 쿼리는 `SomeTable` 매개 변수를 기반으로 하는 변수 스키마 출력이 있는 원격 함수를 호출 합니다 `tablename` .
이 호출은 규칙 #3를 위반 하므로 유효 하지 않습니다.

의 테이블 형식 함수 `OtherDb`

```kusto
.create function SomeTable(tablename:string) { table(tablename)  }  
```

기본 데이터베이스에 있습니다.

```kusto
cluster("OtherCluster").database("OtherDb").SomeTable("MyTable")
```

다음 쿼리는 데이터를 기반으로 하는 변수 스키마 출력이 있는 원격 함수를 호출 합니다 `GetDataPivot` ([pivot () 플러그 인](pivotplugin.md) 은 동적 출력).
이 호출은 규칙 #3를 위반 하므로 유효 하지 않습니다.

의 테이블 형식 함수 `OtherDb`

```kusto
.create function GetDataPivot() { T | evaluate pivot(PivotColumn) }  
```

기본 데이터베이스의 테이블 형식 함수

```kusto
cluster("OtherCluster").database("OtherDb").GetDataPivot()
```

## <a name="displaying-data"></a>데이터 표시

클라이언트에 데이터를 반환 하는 문은 연산자를 특정 하 게 사용 하지 않아도 반환 되는 레코드 수에 의해 암시적으로 제한 됩니다 `take` . 이 제한을 올리려면 `notruncation` 클라이언트 요청 옵션을 사용합니다.

데이터를 그래픽 형식으로 표시 하려면 [render 연산자](renderoperator.md)를 사용 합니다.

::: zone-end

::: zone pivot="azuremonitor"

데이터베이스 간 및 클러스터 간 쿼리는 Azure Monitor에서 지원 되지 않습니다.

::: zone-end
