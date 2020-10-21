---
title: substring ()-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 부분 문자열 ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 3780aac9ad2675e901ffff63a89177b478d461ea
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92251388"
---
# <a name="substring"></a>substring()

일부 인덱스부터 문자열 끝 까지의 소스 문자열에서 부분 문자열을 추출 합니다.

선택적으로 요청된 부분 문자열의 길이를 지정할 수 있습니다.

```kusto
substring("abcdefg", 1, 2) == "bc"
```

## <a name="syntax"></a>구문

`substring(`*원본* `,` *startingIndex* [ `,` *길이*]`)`

## <a name="arguments"></a>인수

* *source*: 부분 문자열을 가져올 원본 문자열입니다.
* *startingIndex*: 요청 된 부분 문자열의 0부터 시작 하는 문자 위치입니다.
* *length*: 부분 문자열에서 요청 된 문자 수를 지정 하는 데 사용할 수 있는 선택적 매개 변수입니다. 

**참고**

*startingIndex* 는 음수가 될 수 있으며,이 경우에는 소스 문자열의 끝에서 부분 문자열이 검색 됩니다.

## <a name="returns"></a>반환

지정된 문자열에서 나온 부분 문자열입니다. 부분 문자열은 startingIndex(0 기반) 문자 위치에서 시작하며 문자열의 끝 또는 length 문자(지정된 경우)까지 계속합니다.

## <a name="examples"></a>예

```kusto
substring("123456", 1)        // 23456
substring("123456", 2, 2)     // 34
substring("ABCD", 0, 2)       // AB
substring("123456", -2, 2)    // 56
```