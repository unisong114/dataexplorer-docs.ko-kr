---
title: strlen ()-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 strlen ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 4256218669685ec12a939156021803105e5ddfc6
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92248405"
---
# <a name="strlen"></a>strlen()

입력 문자열의 길이 (문자)를 반환 합니다.

## <a name="syntax"></a>구문

`strlen(`*원본*`)`

## <a name="arguments"></a>인수

* *source*: 문자열 길이를 측정 하는 소스 문자열입니다.

## <a name="returns"></a>반환

입력 문자열의 길이 (문자)를 반환 합니다.

**참고**

문자열의 각 유니코드 문자는 서로게이트를 포함 하 여와 동일 합니다 `1` .
(예: 중국어 문자는 u t f-8 인코딩의 두 개 이상의 값을 필요로 한다는 점에 불구 하 고 한 번 계산 됩니다.)


## <a name="examples"></a>예

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