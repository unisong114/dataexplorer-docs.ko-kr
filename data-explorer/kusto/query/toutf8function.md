---
title: to_utf8 ()-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기에서 to_utf8 ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: c47c37dbbde7bd2276f1b5788dc6eb7b062f39a3
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92251269"
---
# <a name="to_utf8"></a>to_utf8()

입력 문자열의 유니코드 문자 (make_string의 역 작업)에 대 한 동적 배열을 반환 합니다.

## <a name="syntax"></a>구문

`to_utf8(`*원본*`)`

## <a name="arguments"></a>인수

* *source*: 변환할 소스 문자열입니다.

## <a name="returns"></a>반환

이 함수에 제공 된 문자열을 구성 하는 유니코드 문자의 동적 배열을 반환 합니다.
참조 [`make_string()`](makestringfunction.md) )

## <a name="examples"></a>예

```kusto
print arr = to_utf8("⒦⒰⒮⒯⒪")
```

|arr|
|---|
|[9382, 9392, 9390, 9391, 9386]|

```kusto
print arr = to_utf8("קוסטו - Kusto")
```

|arr|
|---|
|[1511, 1493, 1505, 1496, 1493, 32, 45, 32, 75, 117, 115, 116, 111]|

```kusto
print str = make_string(to_utf8("Kusto"))
```

|str|
|---|
|Kusto|
