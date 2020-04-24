---
title: 스트렙() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 strrep()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 39b398e8fadb400c25cfeb97487c2ecf0669ad83
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81506718"
---
# <a name="strrep"></a>strrep()

주어진 [문자열이](./scalar-data-types/string.md) 제공 된 시간을 반복합니다.

* 첫 번째 또는 세 번째 인수가 문자열 형식이 아닌 경우 강제로 문자열로 변환됩니다.

**구문**

`strrep(`*값,**승수,[구분**기호]*`)`

**인수**

* *값*: 입력 식
* *승수*: 양수 정수 값(1에서 1024까지)
* *구분 기호*: 선택적 문자열 식(기본값: 빈 문자열)

**반환**

지정된 횟수동안 반복되는 값은 *구분 기호와*연결됩니다.

*승수가* 최대 허용 값(1024)보다 큰 경우 입력 문자열은 1024번 반복됩니다.
 
**예제**

```kusto
print from_str = strrep('ABC', 2), from_int = strrep(123,3,'.'), from_time = strrep(3s,2,' ')
```

|from_str|from_int|from_time|
|---|---|---|
|ABCABC|123.123.123|00:00:03 00:00:03|