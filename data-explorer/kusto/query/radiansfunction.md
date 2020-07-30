---
title: radians ()-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 라디안 ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 3aaa41a631498e2938acf722b75f409a1bbe5031
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87345949"
---
# <a name="radians"></a>radians()

수식을 사용 하 여 각도의 각도 값을 라디안으로 값으로 변환 합니다.`radians = (PI / 180 ) * angle_in_degrees`

## <a name="syntax"></a>Syntax

`radians(`*은*`)`

## <a name="arguments"></a>인수

* *a*: 각도 (실제 숫자)입니다.

## <a name="returns"></a>반환

* 각도로 지정 된 각도의 해당 각도 (라디안)입니다. 

## <a name="examples"></a>예제

```kusto
print radians0 = radians(90), radians1 = radians(180), radians2 = radians(360) 

```

|radians0|radians1|radians2|
|---|---|---|
|1.5707963267949|3.14159265358979|6.28318530717959|