---
title: pack_all ()-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기에서 pack_all ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 547c9960d9a9f04e57f1b5ff0cdebada449b1b4b
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92249711"
---
# <a name="pack_all"></a>pack_all()

`dynamic`테이블 형식 식의 모든 열에서 개체 (속성 모음)를 만듭니다.

> [!NOTE]
> 반환 된 개체의 표현은 실행 간에 바이트 수준의 호환성이 보장 되지 않습니다. 예를 들어 모음에 표시 되는 속성은 다른 순서로 표시 될 수 있습니다.

## <a name="syntax"></a>구문

`pack_all()`

## <a name="examples"></a>예

지정 된 테이블 SmsMessages 

|SourceNumber |TargetNumber| CharsCount
|---|---|---
|555-555-1234 |555-555-1212 | 46 
|555-555-1234 |555-555-1213 | 50 
|555-555-1212 |555-555-1234 | 32 

다음 쿼리:

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(SourceNumber:string,TargetNumber:string,CharsCount:long)
[
'555-555-1234','555-555-1212',46,
'555-555-1234','555-555-1213',50,
'555-555-1212','555-555-1234',32
]
| extend Packed=pack_all()
```

HRESULT = NO_ERROR를

|TableName |SourceNumber |TargetNumber | 채워집니다
|---|---|---|---
|SmsMessages|555-555-1234 |555-555-1212 | {"SourceNumber": "555-555-1234", "TargetNumber": "555-555-1212", "CharsCount": 46}
|SmsMessages|555-555-1234 |555-555-1213 | {"SourceNumber": "555-555-1234", "TargetNumber": "555-555-1213", "CharsCount": 50}
|SmsMessages|555-555-1212 |555-555-1234 | {"SourceNumber": "555-555-1212", "TargetNumber": "555-555-1234", "CharsCount": 32}
