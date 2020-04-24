---
title: exp10() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기에서 exp10()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/25/2019
ms.openlocfilehash: 60e5ea9baa994ef131506d755fcf3d600b5f6dfb
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81515643"
---
# <a name="exp10"></a>exp10()

x의 기본-10 지수 함수는 10이 힘 x: 10^x로 상승합니다.  

**구문**

`exp10(`*Ⅹ*`)`

**인수**

* *x*: 실제 숫자, 지수의 값입니다.

**반환**

* x의 지수 값입니다.
* 자연(기본-10) 로그 할림의 경우 [log10()](log10-function.md)을 참조하십시오.
* 베이스-e 및 베이스-2 로그리스름의 지수 함수는 [exp()](exp-function.md)및 [exp2()](exp2-function.md) 참조