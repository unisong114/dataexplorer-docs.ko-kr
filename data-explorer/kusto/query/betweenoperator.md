---
title: between 연산자-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 between 연산자에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.localizationpriority: high
ms.openlocfilehash: 8bb2049c7bc7c81092eb137c820f650bf88abc4e
ms.sourcegitcommit: 4e811d2f50d41c6e220b4ab1009bb81be08e7d84
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2020
ms.locfileid: "95512981"
---
# <a name="between-operator"></a>between 연산자

포함 범위 내의 입력과 일치합니다.

```kusto
Table1 | where Num1 between (1 .. 10)
Table1 | where Time between (datetime(2017-01-01) .. datetime(2017-01-01))
```

`between` 는 임의의 숫자, datetime 또는 timespan 식에 대해 작동할 수 있습니다.
 
## <a name="syntax"></a>구문

*T* `|` `where` *expr* `between` `(` *leftRange* ` .. ` *rightRange*`)`   
 
*Expr* 식이 datetime 인 경우 다른 구문 sugar 구문이 제공 됩니다.

*T* `|` `where` *expr* `between` `(` *leftRangeDateTime* ` .. ` *rightRangeTimespan*`)`   

## <a name="arguments"></a>인수

* *T* -레코드를 일치 시킬 테이블 형식 입력입니다.
* *expr* -필터링 할 식입니다.
* *leftRange* -왼쪽 범위의 식 (포함)입니다.
* *rightRange* -오른쪽 범위의 식 (포함)입니다.

## <a name="returns"></a>반환

( *T* *Expr*  >=  *leftRange* 및 *expr*  <=  *rightRange*)의 조건자가로 계산 되는 T의 행 `true` 입니다.

## <a name="examples"></a>예  

**' Between ' 연산자를 사용 하 여 숫자 값 필터링**  

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
range x from 1 to 100 step 1
| where x between (50 .. 55)
```

|x|
|---|
|50|
|51|
|52|
|53|
|54|
|55|

**' Between ' 연산자를 사용 하 여 datetime 필터링**  

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents
| where StartTime between (datetime(2007-07-27) .. datetime(2007-07-30))
| count 
```

|개수|
|---|
|476|

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents
| where StartTime between (datetime(2007-07-27) .. 3d)
| count 
```

|개수|
|---|
|476|
