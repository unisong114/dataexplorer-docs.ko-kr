---
title: 쿼리 매개 변수 선언 문-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 쿼리 매개 변수 선언 문에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: d9c5e3b41eef933477981945b22b3aceef958dd8
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92251657"
---
# <a name="query-parameters-declaration-statement"></a>쿼리 매개 변수 선언문

::: zone pivot="azuredataexplorer"

Kusto에 전송 된 쿼리에는 이름 또는 값 쌍 집합이 포함 될 수 있습니다. 이 쌍을 쿼리 텍스트 자체와 함께 *쿼리 매개 변수*라고 합니다. 쿼리는 *쿼리 매개 변수 선언 문에서*이름과 유형을 지정 하 여 하나 이상의 값을 참조할 수 있습니다.

쿼리 매개 변수에는 두 가지 주요 사용이 있습니다.

* 삽입 공격에 대 한 보호 메커니즘으로.
* 쿼리를 매개 변수화 하는 방법입니다.

특히, Kusto로 보내는 쿼리에서 사용자가 제공한 입력을 결합 하는 클라이언트 응용 프로그램은이 메커니즘을 사용 하 여 [SQL 삽입](https://en.wikipedia.org/wiki/SQL_injection) 공격에 해당 하는 kusto를 보호 해야 합니다.

## <a name="declaring-query-parameters"></a>쿼리 매개 변수 선언

쿼리 매개 변수, 쿼리 텍스트 또는 쿼리 텍스트를 참조 하려면에서 사용 하는 쿼리 매개 변수를 먼저 선언 해야 합니다. 각 매개 변수에 대해 선언은 이름 및 스칼라 형식을 제공 합니다. 필요에 따라 매개 변수는 기본값을 가질 수도 있습니다. 요청에서 매개 변수에 대 한 구체적인 값을 제공 하지 않으면 기본값이 사용 됩니다. 그런 다음, kusto는 해당 형식에 대 한 일반적인 구문 분석 규칙에 따라 쿼리 매개 변수의 값을 구문 분석 합니다.

## <a name="syntax"></a>구문

`declare``query_parameters` `(` *Name1* `:` *Type1* [ `=` *DefaultValue1*] [ `,` ...]`);`

* *Name1*: 쿼리에서 사용 되는 쿼리 매개 변수의 이름입니다.
* *Type1*: 또는와 같은 해당 형식 `string` `datetime` 입니다.
  사용자가 제공 하는 값은 문자열로 인코딩되고 Kusto는 쿼리 매개 변수에 적절 한 구문 분석 메서드를 적용 하 여 강력한 형식의 값을 가져옵니다.
* *DefaultValue1*: 매개 변수에 대 한 선택적 기본값입니다. 이 값은 적절 한 스칼라 형식의 리터럴이어야 합니다.

> [!NOTE]
> [사용자 정의 함수와](functions/user-defined-functions.md)마찬가지로 형식의 쿼리 매개 변수는 `dynamic` 기본값을 가질 수 없습니다.

## <a name="examples"></a>예

```kusto
declare query_parameters(UserName:string, Password:string);
print n=UserName, p=hash(Password)
```

```kusto
declare query_parameters(percentage:long = 90);
T | where Likelihood > percentage
```

## <a name="specifying-query-parameters-in-a-client-application"></a>클라이언트 응용 프로그램에서 쿼리 매개 변수 지정

쿼리 매개 변수의 이름과 값은 `string` 쿼리를 만드는 응용 프로그램에서 값으로 제공 됩니다. 이름을 반복할 수 없습니다.

값의 해석은 쿼리 매개 변수 선언 문에 따라 수행 됩니다. 모든 값은 쿼리 본문의 리터럴인 것 처럼 구문 분석 됩니다. 구문 분석은 쿼리 매개 변수 선언 문에 지정 된 형식에 따라 수행 됩니다.

### <a name="rest-api"></a>REST API

쿼리 매개 변수는 `properties` 라는 중첩 된 속성 모음에서 요청 본문의 JSON 개체 슬롯을 통해 클라이언트 응용 프로그램에 의해 제공 됩니다 `Parameters` . 예를 들어, 다음은 응용 프로그램에서 사용자의 생일을 요청 하 여 사용자의 연령를 계산 하는 Kusto에 대 한 REST API 호출 본문입니다.

``` json
{
    "ns": null,
    "db": "myDB",
    "csl": "declare query_parameters(birthday:datetime); print strcat(\"Your age is: \", tostring(now() - birthday))",
    "properties": "{\"Options\":{},\"Parameters\":{\"birthday\":\"datetime(1970-05-11)\",\"courses\":\"dynamic(['Java', 'C++'])\"}}"
}
```

### <a name="kusto-net-sdk"></a>Kusto .NET SDK

Kusto .NET 클라이언트 라이브러리를 사용 하는 경우 쿼리 매개 변수의 이름과 값을 제공 하기 위해 개체의 새 인스턴스를 만든 `ClientRequestProperties` 다음, 및 메서드를 사용 하 여 `HasParameter` `SetParameter` `ClearParameter` 쿼리 매개 변수를 조작 합니다. 이 클래스는에 대 한 여러 강력한 형식의 오버 로드를 제공 합니다 `SetParameter` . 내부적으로는 쿼리 언어의 적절 한 리터럴을 생성 하 고 `string` 위에서 설명한 대로 REST API를 통해 보냅니다. 쿼리 텍스트 자체는 여전히 [쿼리 매개 변수를 선언](#declaring-query-parameters)해야 합니다.

### <a name="kustoexplorer"></a>Kusto.Explorer

서비스에 대 한 요청을 만들 때 전송 되는 쿼리 매개 변수를 설정 하려면 **쿼리 매개 변수** "렌치" 아이콘 ()을 사용 `ALT`  +  `P` 합니다.

::: zone-end

::: zone pivot="azuremonitor"

이 기능은에서 지원 되지 않습니다 Azure Monitor

::: zone-end
