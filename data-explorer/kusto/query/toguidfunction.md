---
title: toguid ()-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 toguid ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 689ee6bf7b3fcb27dced20b06a9002659622902e
ms.sourcegitcommit: 3dfaaa5567f8a5598702d52e4aa787d4249824d4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/05/2020
ms.locfileid: "87804137"
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
