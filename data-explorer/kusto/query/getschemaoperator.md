---
title: getschema 연산자-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 getschema 연산자에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 6960a737b0a71a6b921a6a58750e48f5c3fb9da0
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92247369"
---
# <a name="getschema-operator"></a>getschema 연산자 

입력의 테이블 형식 스키마를 나타내는 테이블을 생성 합니다.

```kusto
T | summarize MyCount=count() by Country | getschema 
```

## <a name="syntax"></a>구문

*T* `| ``getschema`

## <a name="example"></a>예제

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents
| top 10 by Timestamp
| getschema
```

|ColumnName|ColumnOrdinal|DataType|ColumnType|
|---|---|---|---|
|타임스탬프|0|System.DateTime|Datetime|
|언어|1|System.String|문자열|
|페이지|2|System.String|문자열|
|뷰|3|System.Int64|long
|BytesDelivered 됨|4|System.Int64|long
