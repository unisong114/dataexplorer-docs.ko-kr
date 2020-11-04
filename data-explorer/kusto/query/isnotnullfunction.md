---
title: isnotnull ()-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 isnotnull ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 44161dcdc34232225f4b133fe0a569fb818c3f0f
ms.sourcegitcommit: 455d902bad0aae3e3d72269798c754f51442270e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93349378"
---
# <a name="isnotnull"></a>isnotnull()

`true`인수가 null이 아닌 경우를 반환 합니다.

## <a name="syntax"></a>구문

`isnotnull(`[ *값* ]`)`

`notnull(`[ *값* ] `)` -별칭 `isnotnull`

## <a name="example"></a>예제

```kusto
T | where isnotnull(PossiblyNull) | count
```
