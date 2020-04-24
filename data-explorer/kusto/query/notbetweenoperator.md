---
title: notbetween 연산자 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기에서 notbetween 연산자에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: eacde679f05ff79f5ee0d223ba005217dbf5192c
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81512090"
---
# <a name="between-operator"></a>연산자 간 !!

포괄 범위를 벗어난 입력과 일치합니다.

```kusto
Table1 | where Num1 !between (1 .. 10)
Table1 | where Time !between (datetime(2017-01-01) .. datetime(2017-01-01))
```

`!between`모든 숫자, 날짜 시간 또는 시간 범위 식에서 작동할 수 있습니다.
 
**구문**

*T* `|` T `where` *expr* `!between` *leftRange*` .. `*rightRange* 왼쪽레인지 오른쪽 범위 `(``)`   
 
*expr* 표현식이 datetime인 경우 - 다른 구문 설탕 구문이 제공됩니다.

*T* `|` T `where` *expr* `!between` ` .. `*rightRangeTimespan* *leftRangeDateTime*왼쪽RangeDateTime 오른쪽 범위 시간 범위 `(``)`   

**인수**

* *T* - 레코드를 일치할 테이블 형식 입력입니다.
* *expr* - 필터링할 식입니다.
* *leftRange* - 왼쪽 범위의 식(포함)입니다.
* *rightRange* - rihgt 범위(포함)의 식입니다.

**반환**

의 술어 *(expr* < *leftRange* 또는 *expr* > *rightRange)가*평가되는 `true` *T의* 행은 .

**예**  

**'!사이' 연산자를 사용하여 숫자 값 필터링**  

```kusto
range x from 1 to 10 step 1
| where x !between (5 .. 9)
```

|x|
|---|
|1|
|2|
|3|
|4|
|10|

**'사이' 연산자사용 날짜 필터링**  


```kusto
StormEvents
| where StartTime !between (datetime(2007-07-27) .. datetime(2007-07-30))
| count 
```

|개수|
|---|
|58590|


```kusto
StormEvents
| where StartTime !between (datetime(2007-07-27) .. 3d)
| count 
```

|개수|
|---|
|58590|