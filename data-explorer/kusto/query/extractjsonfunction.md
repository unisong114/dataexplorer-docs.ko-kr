---
title: extractjson() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기에서 extractjson()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 6177a1c8a6ed4390093e6f6fd24c5f5e9fd04f8a
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81515337"
---
# <a name="extractjson"></a>extractjson()

path 식을 사용하여 JSON 텍스트에서 지정된 요소를 가져옵니다. 

선택적으로 추출한 문자열을 특정 형식으로 변환합니다.

```kusto
extractjson("$.hosts[1].AvailableMB", EventText, typeof(int))
```

**구문**

`extractjson(`*jsonPath* `,` *데이터 소스*`)` 

**인수**

* *jsonPath*: Json 문서에 접근자 정의 JsonPath 문자열입니다.
* *데이터 소스*: JSON 문서입니다.

**반환**

이 함수는 유효한 JSON 문자열을 포함하고 있는 dataSource에 대해 JsonPath 쿼리를 수행하며, 선택적으로 이 값을 세 번째 인수에 따라 다른 형식으로 변환합니다.

**예제**

`[`대괄호`]` 표기및`.`점 () 표기는 동일합니다.

```kusto
T 
| extend AvailableMB = extractjson("$.hosts[1].AvailableMB", EventText, typeof(int)) 

T
| extend AvailableMD = extractjson("$['hosts'][1]['AvailableMB']", EventText, typeof(int)) 
```

### <a name="json-path-expressions"></a>JSON Path 식

|||
|---|---|
|`$`|루트 개체|
|`@`|현재 개체|
|`.` 또는 `[ ]` | 자식|
|`[ ]`|배열 첨자|

*(현재 와일드카드, 재귀, 합집합 또는 조각이 구현되지 않았습니다.)*


**성능 팁**

* `extractjson()`
* 정규식 일치 사용은 [extract](extractfunction.md) 를 대신 사용하는 것으로 간주합니다. 이렇게 하면 훨씬 더 빠르게 실행될 수 있으며 JSON이 템플릿에서 생성된 경우 효과적입니다.
* JSON에서 값을 둘 이상 추출해야 하는 경우 `parse_json()`을 사용합니다.
* 열 형식을 동적으로 선언하여 수집 시 JSON이 구문 분석되게 하는 것으로 간주합니다.