---
title: hash_md5 ()-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 hash_md5 ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 06/29/2020
ms.openlocfilehash: 17b3e0d13f8048ccac90add4eee6f02f3d2e2959
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87346799"
---
# <a name="hash_md5"></a>hash_md5()

입력 값에 대 한 MD5 해시 값을 반환 합니다.

## <a name="syntax"></a>Syntax

`hash_md5(`*원본*`)`

## <a name="arguments"></a>인수

* *source*: 해시할 값입니다.

## <a name="returns"></a>반환

16 진수 문자열로 인코딩된 지정 된 스칼라의 MD5 해시 값입니다 .이 값은 문자 문자열 (0에서 255 사이의 단일 16 진수를 나타냄)입니다.

> [!WARNING]
> 이 함수에서 사용 하는 알고리즘 (MD5)은 나중에 수정 되지 않지만 계산 하기 매우 복잡 합니다. 단일 쿼리 기간 동안 "경량" 해시 함수를 사용 해야 하는 사용자는 대신 함수 [해시 ()](./hashfunction.md) 를 사용 하는 것이 좋습니다.

## <a name="examples"></a>예제

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print 
h1=hash_md5("World"),
h2=hash_md5(datetime(2020-01-01))
```

|h1|h2|
|---|---|
|f5a7924e621e84c9280a9a27e1bcb7f6|786c530672d1f8db31fee25ea8a9390b|


다음 예에서는 함수를 사용 하 여 `hash_md5()` 상태 MD5 해시 값을 기준으로 StormEvents를 집계 합니다. 

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| summarize StormCount = count() by State, StateHash=hash_md5(State)
| top 5 by StormCount
```

|시스템 상태|StateHash|StormCount|
|---|---|---|
|텍사스|3b00dbe6e07e7485a1c12d36c8e9910a|4701|
|캔자스|e1338d0ac8be43846cf9ae967bd02e7f|3166|
|아이오와|6d4a7c02942f093576149db764d4e2d2|2337|
|ILLINOIS|8c00d9e0b3fcd55aed5657e42cc40cf1|2022|
|MISSOURI|2d82f0c963c0763012b2539d469e5008|2016|
