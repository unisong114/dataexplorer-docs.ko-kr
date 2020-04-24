---
title: tohex() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기에서 tohex()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 402a0923d4fe760e97fa6098ad955b6c24a5d5ee
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81506123"
---
# <a name="tohex"></a>tohex()

입력을 헥사데피스트 문자열로 변환합니다.

```kusto
tohex(256) == '100'
tohex(-256) == 'ffffffffffffff00' // 64-bit 2's complement of -256
tohex(toint(-256), 8) == 'ffffff00' // 32-bit 2's complement of -256
tohex(256, 8) == '00000100'
tohex(256, 2) == '100' // Exceeds min length of 2, so min length is ignored.
```

**구문**

`tohex(`*예시*`, [`` *MinLength*]`, )'

**인수**

* *예절*: 육수 문자열로 변환되는 int 또는 긴 값입니다.  다른 형식은 지원되지 않습니다.
* *MinLength*: 출력에 포함할 선행 문자 수를 나타내는 숫자 값입니다.  1에서 16 사이의 값이 지원되고 16보다 큰 값은 16으로 잘립니다.  문자열이 선행 문자없이 minLength보다 길면 minLength는 효과적으로 무시됩니다.  음수는 기본 데이터 크기에 의해서만 최소한으로 나타낼 수 있으므로 int(32비트)의 경우 minLength는 최소 8이 되고 긴(64비트)의 경우 최소 16이 됩니다.

**반환**

변환에 성공하면 결과는 문자열 값이 됩니다.
변환에 성공하지 못하면 결과는 null이 됩니다.