---
title: 긴 데이터 유형 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 긴 데이터 형식에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: b8c51f216b8515e483daf64d9783210ff35ceef3
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81509710"
---
# <a name="the-long-data-type"></a>긴 데이터 형식

데이터 `long` 형식은 서명된 64비트 너비의 정수입니다.

## <a name="long-literals"></a>긴 리터럴

데이터 형식의 `long` 리터럴은 다음 구문으로 지정할 수 있습니다.

`long` `(` *값* `)`

*값이* 다음 양식을 취할 수 있는 위치:
* 하나 이상의 숫자이며, 이 경우 리터럴 값은 이러한 숫자의 소수점 표현입니다. 예를 들어 `long(12)` 형식의 `long`숫자 12입니다.
* 접두사 `0x` 뒤에 하나 이상의 육각 숫자가 뒤따릅니다. 예를 들어 `long(0xf)`은 `long(15)`와 같습니다.
* 마이너스 ()`-`기호 다음에 하나 이상의 숫자입니다. 예를 들어 `long(-1)` 숫자에서 형식 `long`중 하나를 뺀 숫자입니다.
* `null`이 경우 `long` 데이터 형식의 null [값입니다.](null-values.md) 따라서 형식의 `long` null 값은 `long(null)`.

Kusto는 처음 두 `long` 형식에 `long(` / `)` 대해서만 접두사/suffi없이 형식의 리터럴을 지원합니다. 따라서, `123` `long`문자 그대로의 `0x123`문자그대로이지만 `-2` 리터럴이 **아닙니다(현재** 는 종자의 리터럴에 `-` `2` 적용되는 불규칙한 함수로 해석된다).
 
육각 문자열로 긴 변환하려면 - [tohex() 함수를](../tohexfunction.md)참조하십시오.