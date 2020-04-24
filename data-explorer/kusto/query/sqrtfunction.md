---
title: sqrt() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기에서 sqrt()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 660235a60893732288a551e1febd9b7b044b4f00
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81507262"
---
# <a name="sqrt"></a>sqrt()

제곱근 함수를 반환합니다.  

**구문**

`sqrt(`*Ⅹ*`)`

**인수**

* *x*: 실수 >= 0입니다.

**반환**

* `sqrt(x) * sqrt(x) == x`
* 인수가 음수이거나 `real` 값으로 변환할 수 없는 경우 `null`입니다. 