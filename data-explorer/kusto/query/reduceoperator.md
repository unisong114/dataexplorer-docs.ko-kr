---
title: 줄임 연산자-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 감소 연산자에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 7d157244a167e1264b454cd8cd3c103e297c3263
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/13/2020
ms.locfileid: "83373045"
---
# <a name="reduce-operator"></a>reduce 연산자

값 유사성을 기준으로 문자열 집합을 그룹화 합니다.

```kusto
T | reduce by LogMessage with threshold=0.1
```

이러한 각 그룹에 대해 그룹을 가장 잘 설명 하는 **패턴** 을 출력 합니다. 예를 들어 와일드 카드를 나타내는로는 고, 그룹의 값 수, 그룹의 `*` **담당자** (그룹의 원래 값 중 하나)를 **사용할 수** 있습니다.

**구문**

*T* `|` `reduce` [ `kind` `=` *ReduceKind*] `by` *Expr* [ `with` [ `threshold` `=` *Threshold*] [ `,` `characters` `=` *Characters*]]

**인수**

* *Expr*: 값으로 계산 되는 식 `string` 입니다.
* *Threshold*: `real` 범위 (0 ..1)의 리터럴입니다. 기본값은 0.1입니다. 큰 입력의 경우 임계값이 작아야 합니다. 
* *문자*: `string` 용어를 중단 하지 않는 문자 목록에 추가할 문자 목록을 포함 하는 리터럴입니다. (예를 들어 `aaa=bbbb` 및를 `aaa:bbb` 각각 전체 용어로 설정 하 고 및를 중단 하는 것이 아니라를 `=` `:` `":="` 문자열 리터럴로 사용 합니다.)
* *ReduceKind*: 감소 버전을 지정 합니다. 인 경우에만 유효한 값은 `source` 입니다.

**반환**

이 연산자는 세 개의 열 ( `Pattern` , `Count` 및)과 그룹 수 만큼의 행을 포함 하는 테이블을 반환 합니다 `Representative` . `Pattern`는 그룹의 패턴 값으로, `*` 임의 삽입 문자열을 나타내는 와일드 카드로 사용 되며, `Count` 연산자에 대 한 입력의 행 수가이 패턴으로 표시 되는 횟수를 계산 하 고, `Representative` 이 그룹에 속하는 입력의 값 중 하나입니다.

`[kind=source]`이 지정 된 경우 연산자는 `Pattern` 기존 테이블 구조에 열을 추가 합니다.
이 버전의 스키마 구문은 나중에 변경 될 수 있습니다.

예를 들어 `reduce by city`의 결과는 포함할 수도 있습니다. 

|패턴     |개수 |Representative|
|------------|------|--------------|
| San *      | 5182 |San Bernard   |
| Saint *    | 2846 |세인트 루시    |
| Moscow     | 3726 |Moscow        |
| \* -on- \* | 2730 |일대일  |
| 파리      | 2716 |파리         |

사용자 지정 토큰화를 사용 하는 다른 예:

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
range x from 1 to 1000 step 1
| project MyText = strcat("MachineLearningX", tostring(toint(rand(10))))
| reduce by MyText  with threshold=0.001 , characters = "X" 
```

|패턴         |개수|Representative   |
|----------------|-----|-----------------|
|MachineLearning*|1000 |MachineLearningX4|

**예**

다음 예에서는 감소 `reduce` 하기 전에 감소 하는 열의 guid가 대체 되는 "삭제 된" 입력에 연산자를 적용 하는 방법을 보여 줍니다.

```kusto
// Start with a few records from the Trace table.
Trace | take 10000
// We will reduce the Text column which includes random GUIDs.
// As random GUIDs interfere with the reduce operation, replace them all
// by the string "GUID".
| extend Text=replace(@"[[:xdigit:]]{8}-[[:xdigit:]]{4}-[[:xdigit:]]{4}-[[:xdigit:]]{4}-[[:xdigit:]]{12}", @"GUID", Text)
// Now perform the reduce. In case there are other "quasi-random" identifiers with embedded '-'
// or '_' characters in them, treat these as non-term-breakers.
| reduce by Text with characters="-_"
```

**참고 항목**

[autocluster](./autoclusterplugin.md)

**참고 사항**

연산자의 구현은 `reduce` 주로 Risto Vaarandi에 의해 [이벤트 로그의 마이닝 패턴에 대 한 데이터 클러스터링 알고리즘](https://ristov.github.io/publications/slct-ipom03-web.pdf)을 기반으로 합니다.
