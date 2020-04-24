---
title: 반복() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기에서 반복()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 0fd944112a64e7400e38c627b7b0651bb6fccd54
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81510424"
---
# <a name="repeat"></a>repeat()

일련의 동일한 값을 보유 하는 동적 배열을 생성 합니다.

**구문**

`repeat(`*값* `,` *수*`)` 

**인수**

* *값*: 결과 배열의 요소 값입니다. *값의* 유형은 부울, 정수, 긴, 실제, datetime 또는 시간 범위일 수 있습니다.   
* *개수*: 결과 배열의 요소 수입니다. *개수는* 정수 번호여야 합니다.
*개수가* 0이면 빈 배열이 반환됩니다.
*개수가* 0보다 작으면 null 값이 반환됩니다. 

**예**

다음 예제는 `[1, 1, 1]`을 반환합니다.

```kusto
T | extend r = repeat(1, 3)
```