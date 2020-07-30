---
title: string_size ()-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기에서 string_size ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 4832d00703ab9e6d1478af5b3f45ec294dfb79ce
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87350913"
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