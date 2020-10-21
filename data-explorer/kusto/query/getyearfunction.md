---
title: getyear ()-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 getyear ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: a820d05b453be7cc991f8068644d33ff990115fc
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92251149"
---
# <a name="getyear"></a>getyear()

인수의 연도 부분을 반환 합니다 `datetime` .

## <a name="example"></a>예제

```kusto
T
| extend year = getyear(datetime(2015-10-12))
// year == 2015
```