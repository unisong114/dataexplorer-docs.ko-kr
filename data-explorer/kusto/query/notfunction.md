---
title: not ()-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 3224c48b963c051d0d65d27a2e64ec8317be30c3
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252181"
---
# <a name="not"></a>not()

인수 값을 반대로 바꿉니다 `bool` .

```kusto
not(false) == true
```

## <a name="syntax"></a>구문

`not(`*expr*`)`

## <a name="arguments"></a>인수

* *expr*: `bool` 반대로 바꿀 식입니다.

## <a name="returns"></a>반환

인수의 역 논리적 값을 반환 합니다 `bool` .