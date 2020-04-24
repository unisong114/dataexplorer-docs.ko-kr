---
title: 로그() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 로그()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/11/2019
ms.openlocfilehash: 7bd3c4f5c6b262587cab2327486945f5d78aaf02
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81513280"
---
# <a name="log"></a>log()

`log()`자연 로그릿함수를 반환합니다.  

**구문**

`log(`*Ⅹ*`)`

**인수**

* *x*: 0을 >.

**반환**

* 자연 로그림은 기본-e 로그리트렘: 자연 지수 함수(exp)의 역입니다.
* `null`인수가 음수 또는 null이거나 `real` 값으로 변환할 수 없는 경우 

**참고 항목**

* 일반적인(기본-10) 로그 할림의 경우 [log10()](log10-function.md)을 참조하십시오.
* 기본-2 로그 할리템의 경우 [log2()](log2-function.md)