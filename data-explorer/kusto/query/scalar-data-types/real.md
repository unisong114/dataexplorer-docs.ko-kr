---
title: 실제 데이터 유형 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 실제 데이터 형식에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: f69b74670fefcff6f6c24d5235f58beb98b0405a
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81509676"
---
# <a name="the-real-data-type"></a>실제 데이터 형식

데이터 `real` 형식은 64비트 너비의 이중 정밀도 부동 소수점 번호를 나타냅니다.

데이터 형식의 `real` 리터럴은 와 동일한 표현을 갖습니다. NET의 `System.Double`. `1.0`및 `0.1` `1e5` 모든 문자 문자 `real`문자입니다.

몇 가지 특별한 리터럴 양식이 있습니다.
* `real(null)`: null [값입니다.](null-values.md)
* `real(nan)`: 번호가 아닌(NaN). 예를 들어 a를 `0.0` 다른 `0.0`로 나눈 결과입니다.
* `real(+inf)`: 포지티브 인피니티. 예를 들어, 로 `1.0` 나눈 `0.0`결과입니다.
* `real(-inf)`: 네거티브 무한대. 예를 들어, 로 `-1.0` 나눈 `0.0`결과입니다.