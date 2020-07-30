---
title: dayofyear ()-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 dayofyear ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 925b65846c6ba32163bd325fd2ee3321bc7fc802
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348465"
---
# <a name="dayofyear"></a>dayofyear()

지정 된 연도의 일 수를 나타내는 정수를 반환 합니다.

```kusto
dayofyear(datetime(2015-12-14))
```

## <a name="syntax"></a>Syntax

`dayofweek(`*a_date*`)`

## <a name="arguments"></a>인수

* `a_date`: `datetime`입니다.

## <a name="returns"></a>반환

`day number`지정 된 연도의입니다.