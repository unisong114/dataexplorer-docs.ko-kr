---
title: getschema 연산자 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기에서 getschema 연산자에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 2faeee575f1af72cfad808253853ae96aba7a28f
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81514368"
---
# <a name="getschema-operator"></a>getschema 연산자 

입력의 테이블 스키마를 나타내는 테이블을 생성합니다.

```kusto
T | summarize MyCount=count() by Country | getschema 
```

**구문**

*T (주)* `| ``getschema`

**예제**

```kusto
StormEvents
| top 10 by Timestamp
| getschema
```

|ColumnName|ColumnOrdinal|DataType|ColumnType|
|---|---|---|---|
|타임스탬프|0|System.DateTime|Datetime|
|언어|1|System.String|문자열|
|호출|2|System.String|문자열|
|보기|3|System.Int64|long
|바이트 배달|4|System.Int64|long