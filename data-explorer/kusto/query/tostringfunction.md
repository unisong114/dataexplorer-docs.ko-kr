---
title: tostring ()-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 tostring ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 2093ff1117cf7744af550cf93c3fe630fa40a6e6
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87340178"
---
# <a name="tostring"></a>tostring()

입력을 문자열 표현으로 변환 합니다.

```kusto
tostring(123) == "123"
```

## <a name="syntax"></a>구문

`tostring(`*`Expr`*`)`

## <a name="arguments"></a>인수

* *`Expr`*: 문자열로 변환 되는 식입니다. 

## <a name="returns"></a>반환

*`Expr`* 값이 null이 아닌 경우 결과는의 문자열 표현이 됩니다 *`Expr`* .
*`Expr`* 값이 null 이면 결과가 빈 문자열이 됩니다.
 