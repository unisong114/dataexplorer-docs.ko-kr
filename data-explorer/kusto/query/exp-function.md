---
title: exp() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 exp()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: fbfc28c79bedfc387661192db10a297a304a7697
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81515694"
---
# <a name="exp"></a>exp()

x의 기본 e 지수 함수는 e가 전원 x: e^x로 상승합니다.  

**구문**

`exp(`*Ⅹ*`)`

**인수**

* *x*: 실제 숫자, 지수의 값입니다.

**반환**

* x의 지수 값입니다.
* 자연(기본 e) 로그 할림의 경우 [log()를](log-function.md)참조하십시오.
* 기본-2 및 기본-10 로거텀스의 지수 함수는 [exp2()](exp2-function.md)및 [exp10()](exp10-function.md) 참조