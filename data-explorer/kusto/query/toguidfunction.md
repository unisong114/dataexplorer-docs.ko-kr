---
title: toguid ()-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 toguid ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: d122833f4797c8503dd41cc8ba861554d6924338
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92250375"
---
# <a name="toguid"></a>toguid()

입력을 표현으로 변환 [`guid`](./scalar-data-types/guid.md) 합니다.

```kusto
toguid("70fc66f7-8279-44fc-9092-d364d70fce44") == guid("70fc66f7-8279-44fc-9092-d364d70fce44")
```

> [!NOTE]
> 가능 하면 [guid ()를](./scalar-data-types/guid.md) 사용 하는 것이 좋습니다.

## <a name="syntax"></a>구문

`toguid(`*Expr*`)`

## <a name="arguments"></a>인수

* *Expr*: 스칼라로 변환 되는 식입니다 [`guid`](./scalar-data-types/guid.md) . 

## <a name="returns"></a>반환

성공적으로 변환 되 면 결과는 스칼라가 됩니다 [`guid`](./scalar-data-types/guid.md) .
변환이 실패 하면 결과는가 됩니다 `null` .
