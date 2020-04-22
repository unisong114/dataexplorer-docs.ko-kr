---
title: parse_xml() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 parse_xml()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: ace60d0f9652d75b0f55cce4b1b622af20b42dc1
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663688"
---
# <a name="parse_xml"></a>parse_xml()

A를 `string` XML 값으로 해석하고 값을 JSON으로 변환하고 값을 `dynamic`로 반환합니다.

**구문**

`parse_xml(`*Xml*`)`

**인수**

* *xml*: XML `string`형식의 값을 나타내는 형식의 식입니다.

**반환**

XML 형식이 유효하지 않은 경우 *xml*또는 null 값으로 결정되는 [동적](./scalar-data-types/dynamic.md) 형식의 개체입니다.

XML을 JSON으로 변환하는 작업은 [xml2json](https://github.com/Cheedoong/xml2json) 라이브러리를 사용하여 수행됩니다.

변환은 다음과 같이 수행됩니다.

XML                                |JSON                                            |액세스 권한
-----------------------------------|------------------------------------------------|--------------         
`<e/>`                             | { "e": null }                                  | o.e
`<e>text</e>`                      | { "e": "텍스트" }                                | o.e
`<e name="value" />`               | { "e":{"@name": "값"} }                     | o.e["@name"]
`<e name="value">text</e>`         | { "e":@name{" "값", "#text": "텍스트" } | o.e["@name"] o.e["#text"]
`<e> <a>text</a> <b>text</b> </e>` | { "e": { "a": "텍스트", "b": "텍스트" }          | o.e.e.b
`<e> <a>text</a> <a>text</a> </e>` | { "e": { "a": ["텍스트", "텍스트"] } }             | o.a.a[0] o.e.a[1]
`<e> text <a>text</a> </e>`        | { "e": { "#text": "텍스트", "a": "텍스트" }      | 1'e["#text"]

**참고 사항**

* 최대 입력 `string` 길이는 `parse_xml` 128KB입니다. 문자열을 더 오래 해석하면 null 개체가 생성됩니다. 
* 요소 노드, 특성 및 텍스트 노드만 변환됩니다. 다른 모든 것은 건너뜁니다.
 
**예제**

다음 예제에서 `context_custom_metrics`가 `string`인 경우 다음과 유사합니다.
<!--check this code formatting-->

```xml
<?xml version="1.0" encoding="UTF-8"?>
<duration>
    <value>118.0</value>
    <count>5.0</count>
    <min>100.0</min>
    <max>150.0</max>
    <stdDev>0.0</stdDev>
    <sampledValue>118.0</sampledValue>
    <sum>118.0</sum>
</duration>
```

다음 CSL 조각은 XML을 다음 JSON으로 변환합니다.

```json
{
    "duration": {
        "value": 118.0,
        "count": 5.0,
        "min": 100.0,
        "max": 150.0,
        "stdDev": 0.0,
        "sampledValue": 118.0,
        "sum": 118.0
    }
}
```

`duration` 개체에서 슬롯 값을 검색하고, 그로부터 두 개의 `duration.value` 슬롯및 `duration.min` (및`118.0` `110.0`) 를 각각 검색합니다.

```kusto
T
| extend d=parse_xml(context_custom_metrics) 
| extend duration_value=d.duration.value, duration_min=d["duration"]["min"]
```