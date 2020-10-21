---
title: hash_sha256 ()-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 hash_sha256 ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 3d6cd8b71ac5abed2d56e567c992a15512141063
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92242606"
---
# <a name="hash_sha256"></a>hash_sha256()

입력 값의 sha256 해시 값을 반환 합니다.

## <a name="syntax"></a>구문

`hash_sha256(`*원본*`)`

## <a name="arguments"></a>인수

* *source*: 해시할 값입니다.

## <a name="returns"></a>반환

16 진수 문자열로 인코딩된 지정 된 스칼라의 sha256 해시 값입니다 .이 값은 문자 문자열 (0에서 255 사이의 단일 16 진수를 나타냄)입니다.

> [!WARNING]
> 이 함수 (SHA256)에서 사용 하는 알고리즘은 나중에 수정 되지 않지만 계산 하기는 매우 복잡 합니다. 단일 쿼리 기간 동안 "경량" 해시 함수를 사용 해야 하는 사용자는 대신 함수 [해시 ()](./hashfunction.md) 를 사용 하는 것이 좋습니다.

## <a name="examples"></a>예

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print 
h1=hash_sha256("World"),
h2=hash_sha256(datetime(2020-01-01))
```

|h1|h2|
|---|---|
|78ae647dc5544d227130a0682a51e30bc7777fbb6d8a8f17007463a3ecd1d524|ba666752dc1a20eb750b0eb64e780cc4c968bc9fb8813461c1d7e750f302d71d|

다음 예에서는 함수를 사용 하 여 `hash_sha256()` 상태의 SHA256 해시 값을 기준으로 StormEvents를 집계 합니다. 

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents 
| summarize StormCount = count() by State, StateHash=hash_sha256(State)
| top 5 by StormCount desc
```

|시스템 상태|StateHash|StormCount|
|---|---|---|
|텍사스|9087f20f23f91b5a77e8406846117049029e6798ebbd0d38aea68da73a00ca37|4701|
|캔자스|c80e328393541a3181b258cdb4da4d00587c5045e8cf3bb6c8fdb7016b69cc2e|3166|
|아이오와|f85893dca466f779410f65cd904fdc4622de49e119ad4e7c7e4a291ceed1820b|2337|
|ILLINOIS|ae3eeabfd7eba3d9a4ccbfed6a9b8cff269dc43255906476282e0184cf81b7fd|2022|
|MISSOURI|d15dfc28abc3ee73b7d1f664a35980167ca96f6f90e034db2a6525c0b8ba61b1|2016|
