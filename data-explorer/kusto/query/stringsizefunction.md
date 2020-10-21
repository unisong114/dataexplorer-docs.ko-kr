---
title: string_size ()-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기에서 string_size ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: ea06e7e41ebe48e09839109745f3fe7ba5a96e7a
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92251466"
---
# <a name="string_size"></a>string_size()

입력 문자열의 크기 (바이트)를 반환 합니다.

## <a name="syntax"></a>구문

`string_size(`*원본*`)`

## <a name="arguments"></a>인수

* *source*: 문자열 크기를 측정 하는 소스 문자열입니다.

## <a name="returns"></a>반환

입력 문자열의 길이 (바이트)를 반환 합니다.

## <a name="examples"></a>예

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