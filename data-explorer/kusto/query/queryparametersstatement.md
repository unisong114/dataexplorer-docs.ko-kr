---
title: 쿼리 매개 변수 선언 문-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 쿼리 매개 변수 선언 문에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: a76755d04179b3d311e79798162c61db764455d7
ms.sourcegitcommit: d885c0204212dd83ec73f45fad6184f580af6b7e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82737813"
---
# <a name="query-parameters-declaration-statement"></a>쿼리 매개 변수 선언문

::: zone pivot="azuredataexplorer"

Kusto에 전송 된 쿼리는 쿼리 텍스트 자체 뿐만 아니라 쿼리 **매개 변수**라는 이름/값 쌍 집합을 포함할 수 있습니다. 쿼리 텍스트는 쿼리 **매개 변수 선언문**을 통해 이름 및 형식을 지정 하 여 하나 이상의 쿼리 매개 변수 값을 참조할 수 있습니다.

쿼리 매개 변수에는 두 가지 주요 사용이 있습니다.

1. **삽입 공격**에 대 한 보호 메커니즘으로.
2. 쿼리를 매개 변수화 하는 방법입니다.

특히, Kusto로 보내는 쿼리에서 사용자 제공 입력을 결합 하는 클라이언트 응용 프로그램은이 메커니즘을 사용 하 여 [SQL 삽입](https://en.wikipedia.org/wiki/SQL_injection) 공격에 해당 하는 kusto를 보호 해야 합니다.

## <a name="declaring-query-parameters"></a>쿼리 매개 변수 선언

쿼리 매개 변수를 참조할 수 있으려면 쿼리 텍스트 (또는 사용 하는 함수)가 먼저 사용 하는 쿼리 매개 변수를 선언 해야 합니다. 각 매개 변수에 대해 선언은 이름 및 스칼라 형식을 제공 합니다. 필요에 따라 매개 변수에 대 한 구체적인 값을 요청에서 제공 하지 않는 경우 매개 변수에 기본값을 사용할 수도 있습니다. 그런 다음, kusto는 해당 형식에 대 한 일반적인 구문 분석 규칙에 따라 쿼리 매개 변수의 값을 구문 분석 합니다.

**구문**

`declare``query_parameters` `:` *Type1* `,` *Name1* `=` Name1 Type1 [ *DefaultValue1*] [ `(` ...]`);`

* *Name1*: 쿼리에서 사용 되는 쿼리 매개 변수의 이름입니다.
* *Type1*: 해당 형식 (예: `string` `datetime`, 등) 사용자가 제공 하는 값은 문자열로 인코딩되고 Kusto는 쿼리 매개 변수에 적절 한 구문 분석 메서드를 적용 하 여 강력한 형식의 값을 가져옵니다.
* *DefaultValue1*: 매개 변수에 대 한 선택적 기본값입니다. 이는 적절 한 스칼라 형식의 리터럴이어야 합니다.

> [!NOTE]
> [사용자 정의 함수와](functions/user-defined-functions.md)마찬가지로 형식의 `dynamic` 쿼리 매개 변수는 기본값을 가질 수 없습니다.

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

쿼리 매개 변수의 이름과 값은 쿼리를 만드는 응용 `string` 프로그램에서 값으로 제공 됩니다. 이름을 반복할 수 없습니다.

값의 해석은 쿼리 매개 변수 선언 문에 따라 수행 됩니다. 모든 값은 쿼리 매개 변수 선언 문에 지정 된 형식에 따라 쿼리 본문의 리터럴인 것 처럼 구문 분석 됩니다.

### <a name="rest-api"></a>REST API

쿼리 매개 변수는 라는 `properties` `Parameters`중첩 된 속성 모음에서 요청 본문의 JSON 개체 슬롯을 통해 클라이언트 응용 프로그램에 의해 제공 됩니다. 예를 들어, 다음은 응용 프로그램이 사용자에 게 생일을 요청 하도록 하 여 일부 사용자의 연령를 계산 하는 Kusto에 대 한 REST API 호출 본문입니다.

``` json
{
    "ns": null,
    "db": "myDB",
    "csl": "declare query_parameters(birthday:datetime); print strcat(\"Your age is: \", tostring(now() - birthday))",
    "properties": "{\"Options\":{},\"Parameters\":{\"birthday\":\"datetime(1970-05-11)\",\"courses\":\"dynamic(['Java', 'C++'])\"}}"
}
```

### <a name="kusto-net-sdk"></a>Kusto .NET SDK

Kusto .net 클라이언트 라이브러리를 사용 하는 경우 쿼리 매개 변수의 이름과 값을 제공 하기 `ClientRequestProperties` 위해 개체의 새 인스턴스를 만든 다음 `HasParameter`, `SetParameter`및 `ClearParameter` 메서드를 사용 하 여 쿼리 매개 변수를 조작 합니다. 이 클래스는에 대 한 `SetParameter`여러 강력한 형식의 오버 로드를 제공 합니다. 내부적으로는 `string` 쿼리 언어의 적절 한 리터럴을 생성 하 고 위에서 설명한 대로 REST API를 통해 보냅니다. 쿼리 텍스트 자체는 여전히 [쿼리 매개 변수을 선언](#declaring-query-parameters)해야 합니다.

### <a name="kustoexplorer"></a>Kusto.Explorer

서비스에 대 한 요청을 만들 때 전송 되는 쿼리 매개 변수를 설정 하려면 **쿼리 매개 변수** "렌치"`ALT` + `P`아이콘 ()을 사용 합니다.

::: zone-end

::: zone pivot="azuremonitor"

이 기능은에서 지원 되지 않습니다 Azure Monitor

::: zone-end
