---
title: string_size() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기에서 string_size()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 7886e7b8fd5039c9b197ae435bce4f40b93e5038
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81506888"
---
# <a name="string_size"></a>string_size()

입력 문자열의 크기를 바이트로 반환합니다.

**구문**

`string_size(`*소스*`)`

**인수**

* *소스*: 문자열 크기에 대해 측정할 소스 문자열입니다.

**반환**

입력 문자열의 길이(바이트)를 반환합니다.

**예**

```kusto
print size = string_size("hello")
```

|크기|
|---|
|5|

```kusto
print size = string_size("⒦⒰⒮⒯⒪")
```

|크기|
|---|
|15|