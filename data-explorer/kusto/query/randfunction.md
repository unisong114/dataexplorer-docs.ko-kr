---
title: rand ()-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 rand ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 53fc7512c1a0fb2019526f48ade54fabbd351d05
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87345932"
---
# <a name="rand"></a>rand()

난수를 반환 합니다.

```kusto
rand()
rand(1000)
```

## <a name="syntax"></a>Syntax

* `rand()`- `real` [0.0, 1.0) 범위의 균일 한 분포를 사용 하 여 형식의 값을 반환 합니다.
* `rand(`*N* `)` - `real` 집합 {0.0, 1.0, ..., *N* -1}의 균일 한 분포를 사용 하 여 선택한 형식의 값을 반환 합니다.