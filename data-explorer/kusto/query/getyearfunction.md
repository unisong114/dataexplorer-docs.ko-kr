---
title: getyear ()-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 getyear ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 032cc319661218e77d5b23e6c649de7d5856d6c9
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347649"
---
# <a name="getyear"></a>getyear()

인수의 연도 부분을 반환 합니다 `datetime` .

## <a name="example"></a>예제

```kusto
T
| extend year = getyear(datetime(2015-10-12))
// year == 2015
```