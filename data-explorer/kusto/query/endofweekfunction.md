---
title: endofweek ()-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 endofweek ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 57fa1764753e730f9ff0a2b01a70e0c221217d23
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348278"
---
# <a name="endofweek"></a>endofweek()

지정 된 경우 오프셋으로 이동한 날짜를 포함 하는 주의 끝을 반환 합니다.

주의 마지막 요일은 토요일로 간주 됩니다.

## <a name="syntax"></a>Syntax

`endofweek(`*날짜* [ `,` *오프셋*]`)`

## <a name="arguments"></a>인수

* `date`: 입력 날짜입니다.
* `offset`: 입력 날짜 (정수, 기본값-0)의 선택적 오프셋 주 수입니다.

## <a name="returns"></a>반환

지정 된 경우 오프셋을 사용 하 여 지정 된 *날짜* 값에 대 한 주의 끝을 나타내는 날짜/시간입니다.

## <a name="example"></a>예제

```kusto
  range offset from -1 to 1 step 1
 | project weekEnd = endofweek(datetime(2017-01-01 10:10:17), offset)  

```

|주말|
|---|
|2016-12-31 23:59:59.9999999까지|
|2017-01-07 23:59:59.9999999까지|
|2017-01-14 23:59:59.9999999까지|