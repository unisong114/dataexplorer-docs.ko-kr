---
title: 도 ()-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의도 ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 04efbee59bce153de76ab5d44617b8d1bebc121c
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92253079"
---
# <a name="degrees"></a>degrees()

수식을 사용 하 여 라디안의 각도 값을도 값으로 변환 합니다. `degrees = (180 / PI ) * angle_in_radians`

## <a name="syntax"></a>구문

`degrees(`*은*`)`

## <a name="arguments"></a>인수

* *a*: 각도 (실수)입니다.

## <a name="returns"></a>반환

* 라디안으로 지정 된 각도의 해당 각도 (도)입니다. 

## <a name="examples"></a>예

```kusto
print degrees0 = degrees(pi()/4), degrees1 = degrees(pi()*1.5), degrees2 = degrees(0)

```

|degrees0|degrees1|degrees2|
|---|---|---|
|45|270|0|
