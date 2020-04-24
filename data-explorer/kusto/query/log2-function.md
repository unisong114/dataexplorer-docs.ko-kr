---
title: log2() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 log2()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/11/2019
ms.openlocfilehash: 41e9a1457f97fa04a4daa54e1929f27d8a448ae3
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81513127"
---
# <a name="log2"></a>log2()

`log2()`기본-2 로거리hm 함수를 반환합니다.  

**구문**

`log2(`*Ⅹ*`)`

**인수**

* *x*: 0을 >.

**반환**

* 로그는 기본-2 로거리헴: 기본 2가 있는 지수 함수(exp)의 역입니다.
* `null`인수가 음수 또는 null이거나 `real` 값으로 변환할 수 없는 경우 

**참고 항목**

* 자연(기본 e) 로그 할림의 경우 [log()를](log-function.md)참조하십시오.
* 일반적인(기본-10) 로그 할림의 경우 [log10()](log10-function.md)을 참조하십시오.