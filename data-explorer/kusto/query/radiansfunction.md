---
title: radians ()-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 라디안 ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: bacb005b8828c63efac1737c527cc313a3ee052b
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92241323"
---
# <a name="radians"></a>radians()

수식을 사용 하 여 각도의 각도 값을 라디안으로 값으로 변환 합니다. `radians = (PI / 180 ) * angle_in_degrees`

## <a name="syntax"></a>구문

`radians(`*은*`)`

## <a name="arguments"></a>인수

* *a*: 각도 (실제 숫자)입니다.

## <a name="returns"></a>반환

* 각도로 지정 된 각도의 해당 각도 (라디안)입니다. 

## <a name="examples"></a>예

```kusto
print radians0 = radians(90), radians1 = radians(180), radians2 = radians(360) 

```

|radians0|radians1|radians2|
|---|---|---|
|1.5707963267949|3.14159265358979|6.28318530717959|