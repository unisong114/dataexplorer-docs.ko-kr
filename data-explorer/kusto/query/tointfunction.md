---
title: toint ()-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 toint ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 2daea4d190ed349c41a8eecf2eef53b2c2b93716
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87350692"
---
# <a name="toint"></a>toint()

입력을 정수 (부호 있는 32 비트) 숫자 표현으로 변환 합니다.

```kusto
toint("123") == int(123)
```

## <a name="syntax"></a>Syntax

`toint(`*Expr*`)`

## <a name="arguments"></a>인수

* *Expr*: 정수로 변환 되는 식입니다. 

## <a name="returns"></a>반환

성공적으로 변환 되 면 정수가 됩니다.
변환이 성공 하지 못하면 결과는가 됩니다 `null` .
 
*참고*: 가능한 경우 [int ()](./scalar-data-types/int.md) 를 사용 하는 것이 좋습니다.
