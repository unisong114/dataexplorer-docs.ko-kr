---
title: 하위 문자열() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 substring()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: b0273b3e93c8778af9c380f164faec74349aa8cd
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81506701"
---
# <a name="substring"></a>substring()

일부 인덱스에서 문자열끝까지 시작하는 소스 문자열에서 하위 문자열을 추출합니다.

선택적으로 요청된 부분 문자열의 길이를 지정할 수 있습니다.

```kusto
substring("abcdefg", 1, 2) == "bc"
```

**구문**

`substring(`*소스* `,` 시작`,` *인덱스* [ *길이*]`)`

**인수**

* *소스*: 하위 문자열이 가져온 소스 문자열입니다.
* *startingIndex*: 요청된 하위 문자열의 0기반 시작 문자 위치입니다.
* *길이*: 서브스트링에서 요청된 문자 수를 지정하는 데 사용할 수 있는 선택적 매개 변수입니다. 

**참고 사항**

*startingIndex는* 음수일 수 있으며, 이 경우 하위 문자열은 소스 문자열의 끝에서 검색됩니다.

**반환**

지정된 문자열에서 나온 부분 문자열입니다. 부분 문자열은 startingIndex(0 기반) 문자 위치에서 시작하며 문자열의 끝 또는 length 문자(지정된 경우)까지 계속합니다.

**예**

```kusto
substring("123456", 1)        // 23456
substring("123456", 2, 2)     // 34
substring("ABCD", 0, 2)       // AB
substring("123456", -2, 2)    // 56
```