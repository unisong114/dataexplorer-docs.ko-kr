---
title: exp2() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기에서 exp2()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 1302a64d52962d080dcf32403362125e60eceed2
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81515609"
---
# <a name="exp2"></a>exp2()

2의 기본-2 지수 함수인 x는 2로 상승하여 x: 2^x로 상승합니다.  

**구문**

`exp2(`*Ⅹ*`)`

**인수**

* *x*: 실제 숫자, 지수의 값입니다.

**반환**

* x의 지수 값입니다.
* 자연(기본-2) 로그 할림의 경우 [log2()](log2-function.md)을 참조하십시오.
* 베이스-e 및 base-10 로그할림의 지수 함수는 [exp()](exp-function.md)및 [exp10()](exp10-function.md) 참조