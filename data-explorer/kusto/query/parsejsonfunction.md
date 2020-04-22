---
title: parse_json() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 parse_json()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: f87c2155864f039fb5b261786d0fa6eb6770af2f
ms.sourcegitcommit: e94be7045d71a0435b4171ca3a7c30455e6dfa57
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81744685"
---
# <a name="parse_json"></a>parse_json()

를 `string` JSON 값으로 해석하고 값을 `dynamic`로 반환합니다. 

JSON 복합 개체의 하나 이상의 요소를 추출해야 할 때 [extractjson() 함수를](./extractjsonfunction.md) 사용하는 것보다 우수합니다.

**구문**

`parse_json(`*Json*`)`

별칭:
- [todynamic()](./todynamicfunction.md)
- [토이스()](./todynamicfunction.md)

**인수**

* *json*: `string` [JSON 형식의 값을](https://json.org/)나타내는 형식의 식 또는 실제 `dynamic` 값을 나타내는 [동적](./scalar-data-types/dynamic.md)형식의 식입니다.

**반환**

*json의* `dynamic` 값에 의해 결정되는 형식의 개체 :
* *json이* 형식인 `dynamic`경우 해당 값은 있는 것으로 사용됩니다.
* *json이* 형식이고 `string` [적절한 형식의 JSON 문자열인](https://json.org/)경우 문자열이 구문 분석되고 생성된 값이 반환됩니다.
* *json이* 형식이지만 `string` [제대로 형식이 지정된 JSON 문자열이](https://json.org/) **아닌** 경우 반환된 값은 `dynamic` 원래 `string` 값을 보유하는 형식의 개체입니다.

**예제**

다음 예제에서 `context_custom_metrics`가 `string`인 경우 다음과 유사합니다. 

```json
{"duration":{"value":118.0,"count":5.0,"min":100.0,"max":150.0,"stdDev":0.0,"sampledValue":118.0,"sum":118.0}}
```

그런 다음 다음 CSL 조각은 `duration` 개체의 슬롯 값을 검색하고 그로부터 두 개의 `duration.value` `duration.min` 슬롯및 `110.0`(각각)을`118.0` 검색합니다.

```kusto
T
| extend d=parse_json(context_custom_metrics) 
| extend duration_value=d.duration.value, duration_min=d["duration"]["min"]
```

**참고 사항**

"슬롯" 중 하나가 다른 JSON 문자열인 속성 백을 설명하는 JSON 문자열이 있는 것은 다소 일반적입니다. 다음은 그 예입니다.

```kusto
let d='{"a":123, "b":"{\\"c\\":456}"}';
print d
```

이러한 경우 두 번 호출할 `parse_json` 뿐만 아니라 두 번째 호출에서 `tostring` 사용되도록 해야 합니다. 그렇지 않으면 두 `parse_json` 번째 호출은 선언된 형식이 `dynamic`다음과 기 때문에 출력에 입력을 있는 것처럼 전달합니다.

```kusto
let d='{"a":123, "b":"{\\"c\\":456}"}';
print d_b_c=parse_json(tostring(parse_json(d).b)).c
```