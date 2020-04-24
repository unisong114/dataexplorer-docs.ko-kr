---
title: has_any 연산자 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 has_any 연산자에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/11/2019
ms.openlocfilehash: 42a181f7c75ef36119f7f2915fd5327d4a656eb8
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81514317"
---
# <a name="has_any-operator"></a>has_any 연산자

`has_any`제공된 값 집합을 기반으로 연산자 필터를 사용할 수 있습니다.

```kusto
Table1 | where col has_any ('value1', 'value2')
```

**구문**

*Scalar 식의* *T* `|` `where` *콜* `has_any` `(`목록`)`   
*T* `|` T `where` *col* `has_any` 콜 `(` *표식 식*`)`   
 
**인수**

* *T* - 레코드를 필터링할 테이블 형식 입력입니다.
* *col* - 필터링할 열입니다.
* *식 목록* - 표, 스칼라 또는 리터럴 식의 쉼표 분리 목록  
* *테이블 형식 식* - 값 집합이 있는 테이블 형식 식(식에 여러 열이 있는 경우 첫 번째 열이 사용됩니다)

**반환**

술어가 있는 *T행*`true`

**참고 사항**

* 식 목록은 최대 값을 `10,000` 생성할 수 있습니다.    
* 테이블 형식 식의 경우 결과 집합의 첫 번째 열이 선택됩니다.   

**예:**  

**`has_any` 연산자의 간단한 사용 :**  

```kusto
StormEvents 
| where State has_any ("CAROLINA", "DAKOTA", "NEW") 
| summarize count() by State
```

|시스템 상태|count_|
|---|---|
|뉴욕|1750|
|노스캐롤라이나|1721|
|사우스다코타 주|1567|
|뉴저지|1044|
|사우스캐롤라이나 주|915|
|북다코타|905|
|뉴멕시코|527|
|뉴햄프셔|394|


**동적 배열 사용:**

```kusto
let states = dynamic(['south', 'north']);
StormEvents 
| where State has_any (states)
| summarize count() by State
```

|시스템 상태|count_|
|---|---|
|노스캐롤라이나|1721|
|사우스다코타 주|1567|
|사우스캐롤라이나 주|915|
|북다코타|905|
|애틀랜틱 사우스|193|
|애틀랜틱 노스|188|