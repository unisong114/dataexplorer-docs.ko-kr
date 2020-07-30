---
title: getschema 연산자-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 getschema 연산자에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 4fe19148ef8f410f04dc68f435734a2c2c425cca
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347683"
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
|Timestamp|0|System.DateTime|Datetime|
|언어|1|System.String|문자열|
|페이지|2|System.String|문자열|
|보기|3|System.Int64|long
|BytesDelivered 됨|4|System.Int64|long
