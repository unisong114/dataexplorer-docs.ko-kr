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
ms.openlocfilehash: 333c4d59a7ed62fd031ab52019c10abd821fd858
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83226757"
---
# <a name="getschema-operator"></a>getschema 연산자 

입력의 테이블 형식 스키마를 나타내는 테이블을 생성 합니다.

```kusto
T | summarize MyCount=count() by Country | getschema 
```

**구문**

*T* `| ``getschema`

**예제**

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
|보기|3|System.Int64|long
|BytesDelivered 됨|4|System.Int64|long
