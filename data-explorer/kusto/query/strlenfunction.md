---
title: 스트렌() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 strlen()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 33bb1ea56ebc03dc7357264bcdf987c191478cbb
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81506871"
---
# <a name="strlen"></a>strlen()

입력 문자열의 길이(문자)를 반환합니다.

**구문**

`strlen(`*소스*`)`

**인수**

* *소스*: 문자열 길이에 대해 측정할 소스 문자열입니다.

**반환**

입력 문자열의 길이(문자)를 반환합니다.

**참고 사항**

문자열의 각 유니코드 문자는 `1`서로게이트를 포함하여 와 같습니다.
(예: UTF-8 인코딩에서 두 개 이상의 값이 필요하지만 한 자면 한 번 계산됩니다.)


**예**

```kusto
print length = strlen("hello")
```

|length|
|---|
|5|

```kusto
print length = strlen("⒦⒰⒮⒯⒪")
```

|length|
|---|
|5|