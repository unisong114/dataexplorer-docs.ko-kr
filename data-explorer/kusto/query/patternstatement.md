---
title: pattern 문-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 pattern 문을 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 03d183bd042bb75d8bb44f530575bd3b91cb2102
ms.sourcegitcommit: 05489ce5257c0052aee214a31562578b0ff403e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88793810"
---
# <a name="pattern-statement"></a>pattern 문

::: zone pivot="azuredataexplorer"

**패턴** 은 미리 정의 된 문자열 튜플을 매개 변수가 없는 함수 본문에 매핑하는 명명 된 뷰 형식 구문입니다. 패턴은 다음 두 가지 측면에서 고유 합니다.

* 패턴은 범위가 지정 된 테이블 참조와 비슷한 구문을 사용 하 여 "호출" 됩니다.
* 패턴에는 매핑될 수 있는 제어 되는 닫힌 인수 값 집합이 있으며, 매핑 프로세스는 Kusto에 의해 수행 됩니다. 패턴이 선언 되었지만 정의 되지 않은 경우 Kusto는 패턴에 대 한 모든 호출을 식별 하 고 오류로 플래그를 지정 합니다. 이 id를 사용 하면 중간 계층 응용 프로그램에서 이러한 패턴을 "해결" 할 수 있습니다.

## <a name="pattern-declaration"></a>패턴 선언

패턴 문은 패턴을 선언 하거나 정의 하는 데 사용 됩니다.
예를 들어를 패턴으로 선언 하는 패턴 문이 `app` 있습니다.

```kusto
declare pattern app;
```

이 문은 Kusto를 `app` 패턴으로 지시 하지만,이 패턴을 해결 하는 방법에 대해서는 kusto에 지시 하지 않습니다. 따라서 쿼리에서이 패턴을 호출 하려고 하면 특정 오류가 발생 하 고 이러한 모든 호출이 나열 됩니다. 예를 들면 다음과 같습니다.

```kusto
declare pattern app;
app("ApplicationX").StartEvents
| join kind=inner app("ApplicationX").StopEvents on CorrelationId
| count
```

이 쿼리는 Kusto에서 다음 패턴 호출을 확인할 수 없음을 나타내는 오류를 생성 합니다. `app("ApplicationX")["StartEvents"]` 및 `app("ApplicationX")["StopEvents"]` .

## <a name="syntax"></a>구문

`declare``pattern` *PatternName*

## <a name="pattern-definition"></a>패턴 정의

패턴 문은 패턴을 정의 하는 데도 사용할 수 있습니다. 패턴 정의에서는 패턴의 가능한 모든 호출을 명시적으로 배치 하 고 해당 하는 테이블 형식 식을 제공 합니다. Kusto는 쿼리를 실행할 때 각 패턴 호출을 해당 하는 패턴 본문으로 바꿉니다. 예를 들면 다음과 같습니다.

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

일치 되는 각 패턴에 대해 제공 되는 식은 테이블 이름 이거나 [let 문에](letstatement.md)대 한 참조입니다.

## <a name="syntax"></a>구문

`declare``pattern` *PatternName*  =  PatternName `(` *ArgName* `:` *Argtype* [ `,` ...] `)` [ `[` *경로 이름* `:` *pathargtype* `]` ]`{`
&nbsp;&nbsp;&nbsp;&nbsp;`(` *ArgValue1* [ `,` *ArgValue2* ] `)` [ `.[` * pathvalue `]` ] `=` `{` *식* `};` &nbsp; &nbsp; &nbsp; &nbsp; [ &nbsp; &nbsp; &nbsp; &nbsp; `(` *ArgValue1_2* [ `,` *ArgValue2_2* ...] `)` [ `.[` *PathValue_2* `]` ] `=` `{` *expression_2* `};` &nbsp; &nbsp; &nbsp; &nbsp; ... &nbsp; &nbsp; &nbsp; &nbsp; ]        `}`

* *PatternName*: pattern 키워드의 이름입니다. 키워드를 정의 하는 구문은 지정 된 키워드를 사용 하 여 모든 패턴 참조를 검색 하는 데만 사용할 수 있습니다.
* *ArgName*: pattern 인수의 이름입니다. 패턴은 하나 이상의 인수 이름을 허용 합니다.
* *Argtype*: pattern 인수의 형식입니다 (현재만 `string` 허용 됨).
* *PathName*: 경로 인수의 이름입니다. 패턴은 0 또는 1 개의 경로 이름을 허용 합니다.
* *Pathtype*: 경로 인수의 유형입니다 (현재 `string` 는 허용 되는 경우에만).
* *ArgValue1*, *ArgValue2*, ...-패턴 인수의 값 (현재 `string` 리터럴만 허용 됨)
* *Pathvalue* -패턴 경로의 값입니다 (현재 리터럴만 `string` 허용 됨).
* *expression*: *식* -테이블 형식 식 (예: `Logs | where Timestamp > ago(1h)` ) 또는 함수를 참조 하는 람다 식입니다.

## <a name="pattern-invocation"></a>패턴 호출

패턴 호출 구문은 범위가 지정 된 테이블 참조 구문과 유사 합니다.

* *PatternName* `(` *ArgValue1* [ `,` *ArgValue2* ] `).` *Pathvalue*
* *PatternName* `(` *ArgValue1* [ `,` *ArgValue2* ] `).["` *Pathvalue*`"]`

## <a name="notes"></a>참고

**시나리오**

Pattern 문은 사용자 쿼리를 수락 하 고이 쿼리를 Kusto로 보내는 중간 계층 응용 프로그램용으로 설계 되었습니다. 이러한 응용 프로그램은 종종 논리적 스키마 모델을 사용 하 여 이러한 사용자 쿼리를 접두사로 사용 합니다. 이 모델은 let 문 집합으로, [restrict 문으로](restrictstatement.md)접미사로 지정 될 [수](letstatement.md)있습니다.

일부 응용 프로그램에는 사용자에 게 제공할 수 있는 구문이 필요 합니다. 구문은 응용 프로그램에서 생성 하는 논리적 스키마에 정의 된 엔터티를 참조 하는 데 사용 됩니다. 그러나 경우에 따라 엔터티를 미리 알 수 없거나 잠재적 엔터티 수가 너무 커서 논리적 스키마에 미리 정의할 수 없습니다.

패턴은 다음과 같은 방식으로이 시나리오를 해결 합니다. 중간 계층 응용 프로그램은 선언 되었지만 정의 되지 않은 모든 패턴을 사용 하 여 Kusto로 쿼리를 보냅니다. 그런 다음, kusto는 쿼리를 구문 분석 합니다. 하나 이상의 패턴 호출이 있는 경우 Kusto는 이러한 모든 호출을 명시적으로 나열 하 여 중간 계층 응용 프로그램에 오류를 반환 합니다. 그러면 중간 계층 응용 프로그램에서 이러한 각 참조를 확인 하 고 쿼리를 다시 실행할 수 있습니다. 이번에는 완전히 구체화 된 패턴 정의를 접두사로 사용 합니다.

**Normalizations**

Kusto는 패턴을 자동으로 정규화 합니다. 예를 들어, 다음은 동일한 패턴의 모든 호출 이며, 하나는 다시 보고 됩니다.

```kusto
declare pattern app;
union
  app("ApplicationX").StartEvent,
  app('ApplicationX').StartEvent,
  app("ApplicationX").['StartEvent'],
  app("ApplicationX").["StartEvent"]
```

즉, 동일한 것으로 간주 되므로 함께 정의할 수 없습니다.

**와일드카드**

Kusto는 특별 한 방식으로 패턴에서 와일드 카드를 처리 하지 않습니다. 예를 들어 다음 쿼리에서는입니다.

```kusto
declare pattern app;
union app("ApplicationX").*
| count
```

Kusto는 누락 된 단일 패턴 호출을 보고 `app("ApplicationX").["*"]` 합니다.

## <a name="examples"></a>예제

단일 패턴 호출 이상으로 쿼리 합니다.

```kusto
declare pattern A
{
    // ...
};
union (A('a1').Text), (A('a2').Text)
```

|앱|SomeText|
|---|---|
|앱 #1|사용 가능한 텍스트는 다음과 같습니다. 1|
|앱 #1|사용 가능한 텍스트는 다음과 같습니다. 2|
|앱 #1|사용 가능한 텍스트는 3입니다.|
|앱 #1|사용 가능한 텍스트는 4입니다.|
|앱 #1|사용 가능한 텍스트는 5입니다.|
|앱 #2|무료 텍스트는 9입니다.|
|앱 #2|무료 텍스트: 8|
|앱 #2|사용 가능한 텍스트는 7입니다.|
|앱 #2|사용 가능한 텍스트는 다음과 같습니다. 6|
|앱 #2|사용 가능한 텍스트는 5입니다.|

```kusto
declare pattern App;
union (App('a1').Text), (App('a2').Text)
```

**의미 오류**:

> SEM0036: 하나 이상의 패턴 참조가 선언 되지 않았습니다. 검색 된 패턴 참조: ["App (' a1 '). [' 텍스트 '] "," 앱 (' a2 '). [' Text '] "].

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

**의미 오류가 반환**됨:

> SEM0036: 하나 이상의 패턴 참조가 선언 되지 않았습니다. 검색 된 패턴 참조: ["App (' a2 '). [' 메트릭 '] "," 앱 (' a3 '). [' 메트릭 '] "].

::: zone-end

::: zone pivot="azuremonitor"

이 기능은에서 지원 되지 않습니다 Azure Monitor

::: zone-end
