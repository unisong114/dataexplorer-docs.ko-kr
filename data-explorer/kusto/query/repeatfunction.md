---
title: repeat ()-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 반복 ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: cfe3c2704f7eb086319770925419a9877e39366b
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92243070"
---
# <a name="repeat"></a>repeat()

일련의 동일한 값을 포함 하는 동적 배열을 생성 합니다.

## <a name="syntax"></a>구문

`repeat(`*값* `,` *개수*`)` 

## <a name="arguments"></a>인수

* *value*: 결과 배열의 요소 값입니다. *값* 의 형식은 부울, 정수, long, real, datetime 또는 timespan이 될 수 있습니다.   
* *count*: 결과 배열의 요소 수입니다. 이 *수는 정수 여야 합니다* .
*Count* 가 0과 같으면 빈 배열이 반환 됩니다.
*Count* 가 0 보다 작은 경우 null 값이 반환 됩니다. 

## <a name="examples"></a>예

다음 예제는 `[1, 1, 1]`을 반환합니다.

```kusto
T | extend r = repeat(1, 3)
```