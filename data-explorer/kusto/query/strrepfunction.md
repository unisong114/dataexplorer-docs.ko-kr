---
title: strrep ()-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 strrep ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: a5ee36d40035ab2afd19af2da2a0cb174ee365ca
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92251443"
---
# <a name="strrep"></a>strrep()

지정 된 [문자열](./scalar-data-types/string.md) 을 지정 된 횟수 만큼 반복 합니다.

* First 또는 세 번째 인수가 문자열 형식이 아닌 경우에는 강제로 문자열로 변환 됩니다.

## <a name="syntax"></a>구문

`strrep(`*value*,*승수*, [*delimiter*]`)`

## <a name="arguments"></a>인수

* *값*: 입력 식
* *승수*: 양의 정수 값 (1 ~ 1024)
* *delimiter*: 선택적 문자열 식입니다 (기본값: 빈 문자열).

## <a name="returns"></a>반환

지정 된 횟수 만큼 반복 되는 값으로, *구분 기호와*연결 됩니다.

*승수* 가 최대 허용 값 (1024)을 초과 하는 경우 입력 문자열이 1024 번 반복 됩니다.
 
## <a name="example"></a>예제

```kusto
print from_str = strrep('ABC', 2), from_int = strrep(123,3,'.'), from_time = strrep(3s,2,' ')
```

|from_str|from_int|from_time|
|---|---|---|
|ABCABC|123.123.123|00:00:03 00:00:03|