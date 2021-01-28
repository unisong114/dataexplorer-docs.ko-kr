---
title: todynamic(), parse_json() 함수 - Azure Data Explorer
description: 이 문서에서는 Azure Data Explorer의 todynamic(), parse_json() 함수에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 01/25/2021
ms.localizationpriority: high
ms.openlocfilehash: 680ce93ec2a3b39d14475689ac80793a02d86547
ms.sourcegitcommit: 8468c6886dc097032ef6db86992a5e8c9b18786e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98911947"
---
# <a name="todynamic-parse_json"></a>todynamic(), parse_json()

`string`을 JSON 값으로 해석하고 값을 `dynamic`으로 반환합니다. 

> [!NOTE]
> `todynamic()` 및 `parse_json()` 함수는 동등하게 해석됩니다.

이 함수는 복합 JSON 개체의 요소를 둘 이상 추출해야 할 때 [extractjson()](./extractjsonfunction.md) 함수보다 더 효율적입니다. 가능하면 [dynamic()](./scalar-data-types/dynamic.md)를 사용하는 것이 좋습니다.

## <a name="syntax"></a>Syntax

`parse_json(`*json*`)`
`todynamic(`*json*`)`

<!-- deprecated aliases: `toobject()` and parsejson() -->

## <a name="arguments"></a>인수

* *json*: `string` 형식의 식입니다. [JSON 형식 값](https://json.org/) 또는 실제 `dynamic` 값을 나타내는 [동적](./scalar-data-types/dynamic.md) 형식의 식을 나타냅니다.

## <a name="returns"></a>반환

*json* 값에 의해 결정되는 `dynamic` 형식의 개체입니다.
* *json* 이 `dynamic` 형식인 경우 해당 값이 그대로 사용됩니다.
* *json* 이 `string` 형식이고 [올바른 형식의 JSON 문자열](https://json.org/)이면 문자열이 구문 분석되고 생성된 값이 반환됩니다.
* *json* 이 `string` 형식이지만 [올바른 형식의 JSON 문자열](https://json.org/)이 아닌 경우 반환되는 값은 원래 `string` 값을 보유하는 `dynamic` 형식의 개체입니다.

## <a name="example"></a>예제

다음 예제에서 `context_custom_metrics`가 `string`인 경우 다음과 유사합니다.

```json
{"duration":{"value":118.0,"count":5.0,"min":100.0,"max":150.0,"stdDev":0.0,"sampledValue":118.0,"sum":118.0}}
```

그러면 다음 CSL 조각은 개체에 있는 `duration` 슬롯의 값을 검색하며 거기서부터 `duration.value` 및 `duration.min`의 두 슬롯을 검색합니다(각각 `118.0` 및 `110.0`).

```kusto
T
| extend d=parse_json(context_custom_metrics) 
| extend duration_value=d.duration.value, duration_min=d["duration"]["min"]
```

**참고**

일반적으로 "슬롯" 중 하나가 다른 JSON 문자열인 속성 모음을 설명하는 JSON 문자열이 있습니다. 

다음은 그 예입니다. 

```kusto
let d='{"a":123, "b":"{\\"c\\":456}"}';
print d
```

이 경우 `parse_json`을 두 번 호출할 뿐만 아니라 두 번째 호출에서 `tostring`이 사용되는지 확인해야 합니다. 그렇지 않으면 선언된 형식이 `dynamic`이기 때문에 `parse_json`에 대한 두 번째 호출은 입력을 그대로 출력에 전달합니다.

```kusto
let d='{"a":123, "b":"{\\"c\\":456}"}';
print d_b_c=parse_json(tostring(parse_json(d).b)).c
```
