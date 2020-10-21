---
title: strcat ()-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 strcat ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 55a52ff4aece3fa1a3197db0fb47984217292136
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92251521"
---
# <a name="strcat"></a>strcat()

1에서 64 사이의 인수를 연결 합니다.

* 인수가 문자열 형식이 아니면 문자열이 강제로 문자열로 변환 됩니다.

## <a name="syntax"></a>구문

`strcat(`*인수 1*, *인수 2*[, *argumentn*]`)`

## <a name="arguments"></a>인수

* *인수 1* ... *argumentn*: 연결할 식입니다.

## <a name="returns"></a>반환

단일 문자열에 연결 된 인수입니다.

## <a name="examples"></a>예
  
   ```kusto
print str = strcat("hello", " ", "world")
```

|str|
|---|
|hello world|
