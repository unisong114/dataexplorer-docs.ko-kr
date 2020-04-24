---
title: 라디안() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 radians()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 0dc04c5f9593b6bd5fc61f57d20819cf7d2a178c
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81510662"
---
# <a name="radians"></a>radians()

수식을 사용하여 각도 값을 각도 값을 라디안에서 값으로 변환합니다.`radians = (PI / 180 ) * angle_in_degrees`

**구문**

`radians(`*a.*`)`

**인수**

* *a*: 각도(실수)입니다.

**반환**

* 각도로 지정된 각도에 대한 라디안의 해당 각도입니다. 

**예**

```kusto
print radians0 = radians(90), radians1 = radians(180), radians2 = radians(360) 

```

|라디안0|라디안1|라디안2|
|---|---|---|
|1.5707963267949|3.14159265358979|6.28318530717959|