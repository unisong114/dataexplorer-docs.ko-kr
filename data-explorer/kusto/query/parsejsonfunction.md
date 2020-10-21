---
title: parse_json ()-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 parse_json ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 0f49ee6763d4afadf12e9e008bd7ea8e61c49acc
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92246097"
---
# <a name="parse_json"></a>parse_json()

는를 `string` JSON 값으로 해석 하 고 값을로 반환 합니다 `dynamic` .

JSON 복합 개체의 요소를 둘 이상 추출 해야 하는 경우이 함수는 [extractjson () 함수](./extractjsonfunction.md) 보다 낫습니다.

## <a name="syntax"></a>구문

`parse_json(`*json*`)`

별칭
- [todynamic()](./todynamicfunction.md)
- [toobject ()](./todynamicfunction.md)

## <a name="arguments"></a>인수

* *json*: 형식의 식 `string` 입니다. [JSON 형식 값](https://json.org/)또는 실제 값을 나타내는 [dynamic](./scalar-data-types/dynamic.md)형식의 식을 나타냅니다 `dynamic` .

## <a name="returns"></a>반환

`dynamic` *Json*값에 의해 결정 되는 형식의 개체입니다.
* *Json* 형식이 인 경우 `dynamic` 해당 값이 있는 그대로 사용 됩니다.
* *Json* 이 형식이 `string` 고가 적절 한 형식의 [json 문자열이](https://json.org/)면 문자열이 구문 분석 되 고 생성 된 값이 반환 됩니다.
* *Json* 이 형식이 `string` 고 [올바른 형식의 json 문자열이](https://json.org/)아닌 경우 반환 되는 값은 `dynamic` 원래 값을 보유 하는 형식의 개체입니다 `string` .

## <a name="example"></a>예제

다음 예제에서 `context_custom_metrics`가 `string`인 경우 다음과 유사합니다.

```json
{"duration":{"value":118.0,"count":5.0,"min":100.0,"max":150.0,"stdDev":0.0,"sampledValue":118.0,"sum":118.0}}
```

그런 다음, 다음 CSL 조각은 개체에서 슬롯의 값을 검색 하 `duration` 고, 여기서는 두 슬롯과 `duration.value` `duration.min` ( `118.0` 각각 및)를 검색 `110.0` 합니다.

```kusto
T
| extend d=parse_json(context_custom_metrics) 
| extend duration_value=d.duration.value, duration_min=d["duration"]["min"]
```

**참고**

일반적으로 "슬롯" 중 하나가 다른 JSON 문자열인 속성 모음을 설명 하는 JSON 문자열이 있습니다. 

예를 들면 다음과 같습니다.

```kusto
let d='{"a":123, "b":"{\\"c\\":456}"}';
print d
```

이 경우 두 번 호출 하는 것은 물론 `parse_json` 두 번째 호출에서가 사용 되는지 확인 하는 데에도 필요 `tostring` 합니다. 그렇지 않으면에 대 한 두 번째 호출은 `parse_json` 선언 된 형식이 이기 때문에 입력을 그대로 출력에 전달 합니다 `dynamic` .

```kusto
let d='{"a":123, "b":"{\\"c\\":456}"}';
print d_b_c=parse_json(tostring(parse_json(d).b)).c
```
