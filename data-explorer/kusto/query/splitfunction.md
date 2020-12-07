---
title: split() - Azure Data Explorer | Microsoft Docs
description: 이 문서에서는 Azure Data Explorer의 split()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.localizationpriority: high
ms.openlocfilehash: 4baae5bee8dd1e85a304be7fb4eae988acc404d8
ms.sourcegitcommit: f49e581d9156e57459bc69c94838d886c166449e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2020
ms.locfileid: "95512947"
---
# <a name="split"></a>split()

지정된 구분 기호에 따라 지정된 문자열을 분할하고 포함된 부분 문자열이 있는 문자열 배열을 반환합니다.

선택적으로 특정 부분 문자열(있는 경우)을 반환할 수 있습니다.

```kusto
split("aaa_bbb_ccc", "_") == ["aaa","bbb","ccc"]
```

## <a name="syntax"></a>Syntax

`split(`*원본*`,` *구분 기호* [`,` *requestedIndex*]`)`

## <a name="arguments"></a>인수

* *원본*: 지정된 구분 기호에 따라 분할될 원본 문자열입니다.
* *delimiter*: 소스 문자열을 분할하기 위해 사용될 구분 기호입니다.
* *requestedIndex*: 선택적 0부터 시작하는 인덱스 `int`입니다. 제공된 경우, 반환되는 문자열 배열은 요청된 부분 문자열(있는 경우)을 포함합니다. 

## <a name="returns"></a>반환

지정된 구분 기호로 구분되는 지정된 소스 문자열의 부분 문자열을 포함하는 문자열의 배열입니다.

## <a name="examples"></a>예

```kusto
print
    split("aa_bb", "_"),           // ["aa","bb"]
    split("aaa_bbb_ccc", "_", 1),  // ["bbb"]
    split("", "_"),                // [""]
    split("a__b", "_"),            // ["a","","b"]
    split("aabbcc", "bb")          // ["aa","cc"]
```