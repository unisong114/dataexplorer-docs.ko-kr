---
title: acos() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 acos()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: aa33714d57b319ba5a775385e8ee7d232addfe9d
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81519332"
---
# <a name="acos"></a>acos()

코신이 지정된 숫자(역작업)인 각도를 [`cos()`](cosfunction.md)반환합니다.

**구문**

`acos(`*Ⅹ*`)`

**인수**

* *x*: 범위의 실제 숫자 [-1, 1].

**반환**

* 의 호 코신의 값`x`
* `null`< `x` -1 `x` 또는 > 1인 경우