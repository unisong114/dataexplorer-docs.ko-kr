---
title: 쿼리 매개 변수 선언 문 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure Data Explorer에서 쿼리 매개 변수 선언 문을 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: b7193ada6967882306d9a977b6c90af8b247036d
ms.sourcegitcommit: 01eb9aaf1df2ebd5002eb7ea7367a9ef85dc4f5d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81765485"
---
# <a name="query-parameters-declaration-statement"></a>쿼리 매개 변수 선언 문

::: zone pivot="azuredataexplorer"

Kusto로 전송된 쿼리에는 쿼리 텍스트 자체 외에도 **쿼리 매개 변수라는**이름/값 쌍 집합이 포함될 수 있습니다. 그런 다음 쿼리 텍스트는 쿼리 매개 변수 선언 문을 통해 이름과 형식을 지정하여 하나 이상의 쿼리 매개 변수 값을 참조할 수 **있습니다.**

쿼리 매개 변수에는 두 가지 주요 용도가 있습니다.

1. **주사 공격에**대한 보호 메커니즘으로 .
2. 쿼리를 매개 변수화하는 방법입니다.

특히 Kusto로 보내는 쿼리에서 사용자 제공 입력을 결합하는 클라이언트 응용 프로그램은 이 메커니즘을 사용하여 [SQL 주입](https://en.wikipedia.org/wiki/SQL_injection) 공격과 동등한 Kusto로부터 보호해야 합니다.

## <a name="declaring-query-parameters"></a>쿼리 매개 변수 선언

쿼리 매개 변수를 참조하려면 쿼리 텍스트(또는 쿼리가 사용하는 함수)가 먼저 사용하는 쿼리 매개 변수를 선언해야 합니다. 각 매개 변수에 대해 선언은 이름과 스칼라 유형을 제공합니다. 선택적으로 요청이 매개 변수에 대한 구체적인 값을 제공하지 않는 경우 매개 변수에 사용할 기본값을 가질 수도 있습니다. 그런 다음 Kusto는 해당 형식에 대한 일반 구문 분석 규칙에 따라 쿼리 매개 변수의 값을 구문 분석합니다.

**구문**

`declare``query_parameters` `,` *Name1* `:` *Type1* `=` *DefaultValue1*Name1 Type1 [ 기본값1] [ [...] `(``);`

* *Name1*: 쿼리에 사용되는 쿼리 매개 변수의 이름입니다.
* *Type1*: 해당 유형(예: `string`등) `datetime` 사용자가 제공하는 값은 문자열로 인코딩되며 Kusto는 쿼리 매개 변수에 적절한 구문 분석 메서드를 적용하여 강력한 형식의 값을 가져옵니다.
* *DefaultValue1*: 매개 변수에 대한 선택적 기본값입니다. 이 문자 그대로 적절한 스칼라 유형이어야 합니다.

> [!NOTE]
> [사용자 정의 함수와](functions/user-defined-functions.md)마찬가지로 형식의 `dynamic` 쿼리 매개 변수에는 기본값이 있을 수 없습니다.

**예**

```kusto
declare query_parameters(UserName:string, Password:string);
print n=UserName, p=hash(Password)
```

```kusto
declare query_parameters(percentage:long = 90);
T | where Likelihood > percentage
```

## <a name="specifying-query-parameters-in-a-client-application"></a>클라이언트 응용 프로그램에서 쿼리 매개 변수 지정

쿼리 매개 변수의 이름과 값은 `string` 쿼리를 만드는 응용 프로그램에서 값으로 제공됩니다. 이름이 반복되지 않을 수 있습니다.

값의 해석은 쿼리 매개 변수 선언 문에 따라 수행됩니다. 모든 값은 쿼리 매개 변수 선언 문에 의해 지정된 형식에 따라 쿼리 본문에 리터럴인 것처럼 구문 분석됩니다.

### <a name="rest-api"></a>REST API

쿼리 매개 변수는 요청 본문의 `properties` JSON 개체의 슬롯을 통해 클라이언트 응용 프로그램에서 입니다. `Parameters` 예를 들어 일부 사용자의 나이를 계산하는 Rest API 호출의 본보기는 다음과 같습니다(아마도 응용 프로그램에서 사용자에게 생일을 요청하도록 하는 것).

``` json
{
    "ns": null,
    "db": "myDB",
    "csl": "declare query_parameters(birthday:datetime); print strcat(\"Your age is: \", tostring(now() - birthday))",
    "properties": "{\"Options\":{},\"Parameters\":{\"birthday\":\"datetime(1970-05-11)\",\"courses\":\"dynamic(['Java', 'C++'])\"}}"
}
```

### <a name="kusto-net-sdk"></a>쿠스토 .NET SDK

Kusto .NET 클라이언트 라이브러리를 사용할 때 쿼리 매개 변수의 이름과 값을 제공하기 `ClientRequestProperties` 위해 개체의 `HasParameter` `SetParameter`새 `ClearParameter` 인스턴스를 만들고 query 매개 변수를 조작하는 방법 및 메서드를 사용합니다. 이 클래스는 에 대해 `SetParameter`강력한 형식의 오버로드를 제공합니다. 내부적으로 쿼리 언어의 적절한 리터럴을 생성하고 위에서 `string` 설명한 대로 REST API를 통해로 보냅니다. 쿼리 텍스트 자체는 여전히 [쿼리 매개 변수를 선언해야](#declaring-query-parameters)합니다.

### <a name="kustoexplorer"></a>Kusto.Explorer

서비스에 요청할 때 전송되는 쿼리 매개 변수를 설정하려면 **쿼리 매개 변수** "렌치" 아이콘()을`ALT` + `P`사용합니다.

::: zone-end

::: zone pivot="azuremonitor"

Azure 모니터에서는 이 성능이 지원되지 않습니다.

::: zone-end
