---
title: 패턴 문 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 패턴 문을 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: d7ad021fe7b458d54beeb24b908420324b45ad39
ms.sourcegitcommit: 01eb9aaf1df2ebd5002eb7ea7367a9ef85dc4f5d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81765657"
---
# <a name="pattern-statement"></a>패턴 문

::: zone pivot="azuredataexplorer"

**패턴은** 미리 정의된 문자열 tuples를 매개 변수 없는 함수 본문에 매핑하는 명명된 뷰와 같은 구문입니다. 패턴은 두 가지 측면에서 고유합니다.

* 패턴은 범위가 있는 테이블 참조와 유사한 구문을 사용하여 "호출"됩니다.
* 패턴에는 매핑할 수 있는 제어된 클로즈엔드 인수 값 집합이 있으며 매핑 프로세스는 Kusto에서 수행합니다. 즉, 패턴이 선언되었지만 정의되지 않은 경우 Kusto는 패턴에 대한 모든 호출을 오류로 식별하고 플래그를 지정하여 중간 계층 응용 프로그램에서 이러한 패턴을 "해결"할 수 있습니다.


## <a name="pattern-declaration"></a>패턴 선언
패턴 문은 패턴을 선언하거나 정의하는 데 사용됩니다.
예를 들어 다음 의 패턴 문은 `app` 패턴으로 선언합니다.

```kusto
declare pattern app;
```

이 문은 Kusto에게 `app` 패턴임을 알려주지만 Kusto에게 패턴을 해결하는 방법을 알려주지 않습니다. 따라서 쿼리에서 이 패턴을 호출하려고 하면 이러한 모든 호출이 나열되는 특정 오류가 발생합니다. 다음은 그 예입니다.

```kusto
declare pattern app;
app("ApplicationX").StartEvents
| join kind=inner app("ApplicationX").StopEvents on CorrelationId
| count
```

이 쿼리는 Kusto에서 오류를 생성하여 다음 패턴 호출을 확인할 `app("ApplicationX")["StartEvents"]` 수 `app("ApplicationX")["StopEvents"]`없음을 나타냅니다.

**구문**

`declare``pattern` *패턴 이름*

## <a name="pattern-definition"></a>패턴 정의

패턴 문을 사용하여 패턴을 정의할 수도 있습니다. 패턴 정의에서 패턴의 가능한 모든 호출이 명시적으로 배치되고 해당 테이블 형식 식이 지정됩니다. 그런 다음 Kusto가 쿼리를 실행하면 각 패턴 호출이 해당 패턴 본문으로 바꿉습니다. 다음은 그 예입니다.

```kusto
declare pattern app = (applicationId:string)[eventType:string]
{
    ("ApplicationX").["StopEvents"] = { database("AppX").Events | where EventType == "StopEvent" };
    ("ApplicationX").["StartEvents"] = { database(applicationId).Events | where EventType == eventType } ;
};
app("ApplicationX").StartEvents
| join kind=inner app("ApplicationX").StopEvents on CorrelationId
| count
```

일치하는 각 패턴에 대해 제공되는 식은 테이블 이름 또는 [let 문에](letstatement.md)대한 참조입니다.

**구문**

`declare``pattern` *패턴 이름* = `(`*ArgName* `:` *ArgType* [ ...`,` ] `)` [`[` *경로 이름* `:` *경로ArgType* `]`]`{`
&nbsp;&nbsp;&nbsp;&nbsp;`(` *아르그밸류1* [`,` *아르그밸류2* ~ ] `)` [ `.[` * `]` 경로 `=` `{`값 ] *표현식* `};` &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; [ `(` *ArgValue1_2* [`,` *ArgValue2_2* ... ] `)` [ `.[` *PathValue_2]* `]` `=` &nbsp; *expression_2* expression_2`};` &nbsp; ... &nbsp; `{` &nbsp; &nbsp;&nbsp;&nbsp;&nbsp; ]        `}`

* *패턴 이름*: 패턴 키워드의 이름입니다. 키워드만 정의하는 구문은 지정된 키워드로 모든 패턴 참조를 검색하는 데 허용됩니다.
* *ArgName*: 패턴 인수의 이름입니다. 패턴은 하나 이상의 인수 이름을 허용합니다.
* *ArgType*: 패턴 인수의 유형 `string` (현재만 허용됨)
* *경로 이름*: 경로 인수의 이름입니다. 패턴은 0 또는 하나의 경로 이름을 허용합니다.
* *경로 유형*: 경로 인수의 `string` 유형 (현재만 허용됨)
* *ArgValue1*, *ArgValue2*, ... - 패턴 인수의 값 (현재 리터럴만 `string` 허용됨)
* *PathValue* - 패턴 경로의 값(현재 리터럴만 `string` 허용됨)
* *식*: *식* - 테이블 형식식(예: `Logs | where Timestamp > ago(1h)`또는 함수를 참조하는 lambda 식)입니다.

## <a name="pattern-invocation"></a>패턴 호출

패턴 호출 구문은 범위가 되는 테이블 참조 구문과 유사합니다.

* *패턴 이름* `(` *ArgValue1* [`,` *ArgValue2* ...] `).` *경로 값*
* *패턴 이름* `(` *ArgValue1* [`,` *ArgValue2* ...] `).["` *경로 값*`"]`

## <a name="remarks"></a>설명

**시나리오**

패턴 문은 사용자 쿼리를 수락한 다음 이러한 쿼리를 Kusto로 보내는 중간 계층 응용 프로그램을 위해 설계되었습니다. 이러한 응용 프로그램은 종종 논리 스키마 [모델(let 문](letstatement.md)집합, 제한 [문으로](restrictstatement.md)접미사)으로 이러한 사용자 쿼리를 접두사로 설정합니다.
경우에 따라 이러한 응용 프로그램은 사용자에게 미리 알 수 없고 생성한 논리 스키마에 정의할 수 없는 엔터티를 참조할 수 있는 구문이 필요합니다(잠재적 엔터티 수가 논리 스키마에서 미리 정의할 수 없을 정도로 많기 때문에 미리 알 수 없기 때문입니다).

패턴은 다음과 같은 방법으로 이 시나리오를 해결합니다. 중간 계층 응용 프로그램은 선언된 모든 패턴을 사용하여 Kusto에 쿼리를 보내지만 정의되지는 않습니다. 그런 다음 Kusto는 쿼리를 구문 분석하고 쿼리에 하나 이상의 패턴 호출이 있는 경우 이러한 모든 호출이 명시적으로 나열된 중간 계층 응용 프로그램에 오류를 반환합니다. 그런 다음 중간 계층 응용 프로그램은 이러한 각 참조를 해결하고 쿼리를 다시 실행할 수 있으며, 이번에는 완전히 정교한 패턴 정의로 접두사에 고정할 수 있습니다.

**정규화**

Kusto는 자동으로 패턴을 정규화하므로 예를 들어 다음의 모든 호출은 동일한 패턴의 호출이며 단일 패턴은 다시 보고됩니다.

```kusto
declare pattern app;
union
  app("ApplicationX").StartEvent,
  app('ApplicationX').StartEvent,
  app("ApplicationX").['StartEvent'],
  app("ApplicationX").["StartEvent"]
```

이것은 또한 그들이 동일하게 간주되기 때문에 함께 정의 할 수 없다는 것을 의미합니다.

**와일드카드**

Kusto는 와일드카드를 특별한 방식으로 취급하지 않습니다. 예를 들어 다음 쿼리에서 다음을 수행합니다.

```kusto
declare pattern app;
union app("ApplicationX").*
| count
```

Kusto는 누락된 패턴 호출을 `app("ApplicationX").["*"]`보고합니다.

## <a name="examples"></a>예

단일 패턴 호출 이상에 대한 쿼리:

```kusto
declare pattern A
{
    // ...
};
union (A('a1').Text), (A('a2').Text)
```

|앱|일부 텍스트|
|---|---|
|앱 #1|이 텍스트는 자유 텍스트입니다: 1|
|앱 #1|이것은 자유 텍스트입니다 : 2|
|앱 #1|이것은 자유 텍스트입니다: 3|
|앱 #1|이것은 자유 텍스트입니다 : 4|
|앱 #1|이것은 자유 텍스트입니다 : 5|
|앱 #2|이것은 자유 텍스트입니다 : 9|
|앱 #2|이것은 자유 텍스트입니다 : 8|
|앱 #2|이것은 자유 텍스트입니다 : 7|
|앱 #2|이것은 자유 텍스트입니다: 6|
|앱 #2|이것은 자유 텍스트입니다 : 5|

```kusto
declare pattern App;
union (App('a1').Text), (App('a2').Text)
```

의미 체계 오류:

     SEM0036: One or more pattern references were not declared. Detected pattern references: ["App('a1').['Text']","App('a2').['Text']"].

```kusto
declare pattern App;
declare pattern App = (applicationId:string)[scope:string]  
{
    ('a1').['Data']    = { range x from 1 to 5 step 1 | project App = "App #1", Data    = x };
    ('a1').['Metrics'] = { range x from 1 to 5 step 1 | project App = "App #1", Metrics = rand() };
    ('a2').['Data']    = { range x from 1 to 5 step 1 | project App = "App #2", Data    = 10 - x };
    ('a3').['Metrics'] = { range x from 1 to 5 step 1 | project App = "App #3", Metrics = rand() };
};
union (App('a2').Metrics), (App('a3').Metrics) 
```

시맨틱 오류가 반환되었습니다.

    SEM0036: One or more pattern references were not declared. Detected pattern references: ["App('a2').['Metrics']","App('a3').['Metrics']"].

::: zone-end

::: zone pivot="azuremonitor"

Azure 모니터에서는 이 성능이 지원되지 않습니다.

::: zone-end
