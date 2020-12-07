---
title: substring() - Azure Data Explorer | Microsoft Docs
description: 이 문서에서는 Azure Data Explorer의 substring()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.localizationpriority: high
ms.openlocfilehash: 7b2f2dc18fe12f4bd07b638b6c3ca32d95a10618
ms.sourcegitcommit: f49e581d9156e57459bc69c94838d886c166449e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2020
ms.locfileid: "95512046"
---
# <a name="substring"></a>substring()

일부 인덱스부터 문자열 끝까지의 원본 문자열에서 부분 문자열을 추출합니다.

선택적으로 요청된 부분 문자열의 길이를 지정할 수 있습니다.

```kusto
substring("abcdefg", 1, 2) == "bc"
```

## <a name="syntax"></a>Syntax

`substring(`*원본*`,` *startingIndex* [`,` *length*]`)`

## <a name="arguments"></a>인수

* *원본*: 부분 문자열을 가져올 원본 문자열입니다.
* *startingIndex*: 요청된 부분 문자열의 0부터 시작하는 시작 문자 위치입니다.
* *length*: 부분 문자열에서 요청된 문자 수를 지정하는 데 사용할 수 있는 선택적 매개 변수입니다. 

**참고**

*startingIndex* 는 음수가 될 수 있으며, 이 경우 원본 문자열의 끝에서 부분 문자열이 검색됩니다.

## <a name="returns"></a>반환

지정된 문자열에서 나온 부분 문자열입니다. 부분 문자열은 startingIndex(0 기반) 문자 위치에서 시작하며 문자열의 끝 또는 length 문자(지정된 경우)까지 계속합니다.

## <a name="examples"></a>예

```kusto
substring("123456", 1)        // 23456
substring("123456", 2, 2)     // 34
substring("ABCD", 0, 2)       // AB
substring("123456", -2, 2)    // 56
```