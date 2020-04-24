---
title: 도() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 degrees()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 1365d6c6629f4f4769d7c4b62491eaec25e4ec59
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81516034"
---
# <a name="degrees"></a>degrees()

수식을 사용하여 라디안의 각도 값을 각도 값으로 도에서 변환합니다.`degrees = (180 / PI ) * angle_in_radians`

**구문**

`degrees(`*a.*`)`

**인수**

* *a*: 라디안의 각도(실수).

**반환**

* 라디안에 지정된 각도에 대한 해당 각도입니다. 

**예**

```kusto
print degrees0 = degrees(pi()/4), degrees1 = degrees(pi()*1.5), degrees2 = degrees(0)

```

|도0|도1|도2|
|---|---|---|
|45|270|0|
