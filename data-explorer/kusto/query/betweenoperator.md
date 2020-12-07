---
title: between 연산자 - Azure Data Explorer
description: 이 문서에서는 Azure Data Explorer의 between 연산자에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.localizationpriority: high
ms.openlocfilehash: 8bb2049c7bc7c81092eb137c820f650bf88abc4e
ms.sourcegitcommit: f49e581d9156e57459bc69c94838d886c166449e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2020
ms.locfileid: "95512981"
---
# <a name="between-operator"></a>between 연산자

포함 범위 내의 입력과 일치합니다.

```kusto
Table1 | where Num1 between (1 .. 10)
Table1 | where Time between (datetime(2017-01-01) .. datetime(2017-01-01))
```

`between`은 임의의 숫자, 날짜/시간 또는 시간 범위 식에서 작동할 수 있습니다.
 
## <a name="syntax"></a>Syntax

*T* `|` `where` *expr* `between` `(`*leftRange*` .. `*rightRange*`)`   
 
*expr* 식이 datetime인 경우 다른 구문인 sugar 구문이 제공됩니다.

*T* `|` `where` *expr* `between` `(`*leftRangeDateTime*` .. `*rightRangeTimespan*`)`   

## <a name="arguments"></a>인수

* *T* - 레코드를 일치시킬 테이블 형식 입력입니다.
* *expr* - 필터링할 식입니다.
* *leftRange* - 왼쪽 범위의 식(포함)입니다.
* *rightRange* - 오른쪽 범위의 식(포함)입니다.

## <a name="returns"></a>반환

(*expr* >= *leftRange* 및 *expr* <= *rightRange*)의 조건자의 *T* 가 있는 행은 `true`로 평가됩니다.

## <a name="examples"></a>예제  

**'between' 연산자를 사용하여 숫자 값 필터링**  

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

**'between' 연산자를 사용하여 datetime 필터링**  

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
