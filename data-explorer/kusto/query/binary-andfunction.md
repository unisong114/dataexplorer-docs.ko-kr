---
title: binary_and ()-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기에서 binary_and ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 611580aebbfd974377f5a22ec904bbbcdbeb6e3f
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349094"
---
# <a name="binary_and"></a>binary_and()

두 값 사이의 비트 연산 결과를 반환 합니다 `and` .

```kusto
binary_and(x,y) 
```

## <a name="syntax"></a>Syntax

`binary_and(`*num1* `,` *num2*`)`

## <a name="arguments"></a>인수

* *num1*, *num2*: long 숫자입니다.

## <a name="returns"></a>반환

값 쌍에 대 한 논리적 AND 연산을 반환 합니다. num1 & num2.