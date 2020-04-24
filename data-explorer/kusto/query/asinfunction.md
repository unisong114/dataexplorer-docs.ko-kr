---
title: asin() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기에서 asin()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 5db112daeba59dd841b02df8ba1a41185654ad6a
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81518550"
---
# <a name="asin"></a>asin()

Sine이 지정된 숫자(역작업)인 각도를 [`sin()`](sinfunction.md)반환합니다.

**구문**

`asin(`*Ⅹ*`)`

**인수**

* *x*: 범위의 실제 숫자 [-1, 1].

**반환**

* 의 호 사이의 값`x`
* `null`< `x` -1 `x` 또는 > 1인 경우