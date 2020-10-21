---
title: parse_xml ()-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기에서 parse_xml ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 95741bb407baec3298bb84695ed98b4adcbbd5a6
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92246163"
---
# <a name="parse_xml"></a>parse_xml()

는를 `string` XML 값으로 해석 하 고, 값을 JSON으로 변환 하 고, 값을로 반환 합니다 `dynamic` .

## <a name="syntax"></a>구문

`parse_xml(`*xml*`)`

## <a name="arguments"></a>인수

* *xml*: `string` xml 형식 값을 나타내는 형식의 식입니다.

## <a name="returns"></a>반환

*Xml*의 값에 의해 결정 되는 [dynamic](./scalar-data-types/dynamic.md) 형식의 개체 이거나, xml 형식이 잘못 된 경우 null입니다.

변환은 다음과 같이 수행 됩니다.

XML                                |JSON                                            |액세스 권한
-----------------------------------|------------------------------------------------|--------------         
`<e/>`                             | {"e": null}                                  | e. o
`<e>text</e>`                      | {"e": "text"}                                | e. o
`<e name="value" />`               | {"e": {" @name ": "값"}}                     | o. e [" @name "]
`<e name="value">text</e>`         | {"e": {" @name ": "값", "#text": "텍스트"}} | $. e [" @name " "] o. e [" #text "]
`<e> <a>text</a> <b>text</b> </e>` | {"e": {"a": "텍스트", "b": "텍스트"}}          | $ ....b
`<e> <a>text</a> <a>text</a> </e>` | {"e": {"a": ["text", "text"]}}             | $ ..a [0] o. e. a [1]
`<e> text <a>text</a> </e>`        | {"e": {"#text": "text", "a": "text"}}      | 1 ' o. e ["#text"] o. e. a

**참고**

* 의 최대 입력 `string` 길이 `parse_xml` 는 1mb (1048576 바이트)입니다. 문자열을 더 길게 해석 하면 null 개체가 생성 됩니다.
* 요소 노드, 특성 및 텍스트 노드만 변환 됩니다. 다른 모든 항목을 건너뜁니다.
 
## <a name="example"></a>예제

다음 예제에서 `context_custom_metrics`가 `string`인 경우 다음과 유사합니다. 

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

그러면 다음 CSL 조각이 XML을 다음 JSON으로 변환 합니다.

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

및는 개체에서 슬롯의 값을 검색 하 고, `duration` 여기서는 두 슬롯과 `duration.value` `duration.min` ( `118.0` 각각 및)를 검색 `100.0` 합니다.

```kusto
T
| extend d=parse_xml(context_custom_metrics) 
| extend duration_value=d.duration.value, duration_min=d["duration"]["min"]
```
