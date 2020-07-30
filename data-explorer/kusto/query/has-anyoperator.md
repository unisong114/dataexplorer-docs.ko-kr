---
title: has_any 연산자-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 has_any 연산자에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/11/2019
ms.openlocfilehash: 4485dde5eb77478e5fd75ce388ada7f4232f2ddb
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347632"
---
# <a name="has_any-operator"></a>has_any 연산자

`has_any`제공 된 값 집합을 기반으로 하는 연산자 필터입니다.

```kusto
Table1 | where col has_any ('value1', 'value2')
```

## <a name="syntax"></a>Syntax

*T* `|` `where` *col* `has_any` `(` *스칼라 식의* T 열 목록`)`   
*T* `|` `where` *col* `has_any` `(` *테이블 형식 식*`)`   
 
## <a name="arguments"></a>인수

* 레코드를 필터링 할 *T* -테이블 형식 입력입니다.
* 필터링 *할 열입니다* .
* *식 목록* -쉼표로 구분 된 테이블 형식, 스칼라 또는 리터럴 식 목록입니다.  
* *테이블 형식 식* -값 집합이 있는 테이블 형식 식입니다. 식에 여러 열이 있는 경우 첫 번째 열이 사용 됩니다.

## <a name="returns"></a>반환

조건자가 인 *T* 의 행`true`

**참고**

* 식 목록에는 최대 값이 생성 될 수 있습니다 `10,000` .    
* 테이블 형식 식의 경우 결과 집합의 첫 번째 열이 선택 됩니다.   

**예:**  

**연산자의 간단한 사용법 `has_any` :**  

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents 
| where State has_any ("CAROLINA", "DAKOTA", "NEW") 
| summarize count() by State
```

|시스템 상태|count_|
|---|---|
|뉴욕|1750|
|북부 CAROLINA|1721|
|남부 노스다코타|1567|
|새 JERSEY|1044|
|남부 CAROLINA|915|
|북부 노스다코타|905|
|새 멕시코|527|
|새 뉴햄프셔|394|


**동적 배열 사용:**

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let states = dynamic(['south', 'north']);
StormEvents 
| where State has_any (states)
| summarize count() by State
```

|시스템 상태|count_|
|---|---|
|북부 CAROLINA|1721|
|남부 노스다코타|1567|
|남부 CAROLINA|915|
|북부 노스다코타|905|
|대서양 남부|193|
|대서양 북부|188|
