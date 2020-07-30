---
title: not ()-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: fed2a55c8fa1c7689c087ccdeaa64ff576bea401
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87346595"
---
# <a name="not"></a>not()

인수 값을 반대로 바꿉니다 `bool` .

```kusto
not(false) == true
```

## <a name="syntax"></a>Syntax

`not(`*expr*`)`

## <a name="arguments"></a>인수

* *expr*: `bool` 반대로 바꿀 식입니다.

## <a name="returns"></a>반환

인수의 역 논리적 값을 반환 합니다 `bool` .