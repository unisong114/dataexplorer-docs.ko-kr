---
title: rand ()-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 rand ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 1206f72b3951601c105de450bc6923861ad011ae
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92241292"
---
# <a name="rand"></a>rand()

난수를 반환 합니다.

```kusto
rand()
rand(1000)
```

## <a name="syntax"></a>구문

* `rand()` - `real` [0.0, 1.0) 범위의 균일 한 분포를 사용 하 여 형식의 값을 반환 합니다.
* `rand(`*N* `)` - `real` 집합 {0.0, 1.0, ..., *N* -1}의 균일 한 분포를 사용 하 여 선택한 형식의 값을 반환 합니다.