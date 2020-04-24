---
title: todynamic(), 토오브젝트() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기에서 todynamic(), toobject()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 138b0f978df699817c5dc5c14bafc4c06a95afc7
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81506157"
---
# <a name="todynamic-toobject"></a>todynamic(), 토오브젝트()

`string` [를 JSON 값으로](https://json.org/) 해석하고 값을 [`dynamic`](./scalar-data-types/dynamic.md)로 반환합니다. 

JSON 복합 개체의 하나 이상의 요소를 추출해야 할 때 [extractjson() 함수를](./extractjsonfunction.md) 사용하는 것보다 우수합니다.

[parse_json()](./parsejsonfunction.md) 함수에 대한 별칭입니다.

**구문**

`todynamic(`*json*`)`
`toobject(`*json*`)`

**인수**

* *json*: JSON 문서.

**반환**

*json*에 의해 지정된 형식 `dynamic`의 개체입니다.

*참고*: 가능하면 [dynamic()를](./scalar-data-types/dynamic.md) 사용하는 것을 선호합니다.