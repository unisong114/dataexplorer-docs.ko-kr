---
title: startofweek ()-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 startofweek ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: d5f53dfca4183c4dae623b41abf4c5bce6a3e828
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92241181"
---
# <a name="startofweek"></a>startofweek()

지정 된 경우 오프셋으로 이동한 날짜를 포함 하는 주의 시작을 반환 합니다.

주의 시작은 일요일이 되는 것으로 간주 됩니다.

## <a name="syntax"></a>구문

`startofweek(`*날짜* [ `,` *오프셋*]`)`

## <a name="arguments"></a>인수

* `date`: 입력 날짜입니다.
* `offset`: 입력 날짜 (정수, 기본값-0)의 선택적 오프셋 주 수입니다.

## <a name="returns"></a>반환

지정 된 경우 오프셋 (지정 된 경우)을 사용 하 여 지정 된 *날짜* 값에 대 한 주의 시작을 나타내는 날짜/시간입니다.

## <a name="example"></a>예제

```kusto
  range offset from -1 to 1 step 1
 | project weekStart = startofweek(datetime(2017-01-01 10:10:17), offset) 
```

|weekStart|
|---|
|2016-12-25 00:00:00.0000000|
|2017-01-01 00:00:00.0000000|
|2017-01-08 00:00:00.0000000|