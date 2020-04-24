---
title: strcat_array() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 strcat_array()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 2d2412762cf68243e3952a8ad12a5b919d947bd3
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81506956"
---
# <a name="strcat_array"></a>strcat_array()

지정된 구분 기호를 사용하여 배열 값의 연결된 문자열을 만듭니다.
    
**구문**

`strcat_array(`*배열*, *구분 기호*`)`

**인수**

* *배열*: `dynamic` 연결될 값의 배열을 나타내는 값입니다.
* *섬세계*: `string` *배열의* 값을 통합하는 데 사용되는 값

**반환**

단일 문자열에 연결된 배열 값입니다.

**예**
  
```kusto
print str = strcat_array(dynamic([1, 2, 3]), "->")
```

|str|
|---|
|1->2->3|