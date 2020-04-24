---
title: 연산자 줄이기 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기에서 연산자 줄이기에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 33d4ac202b61fdaa1b92291407cdd2783d947c6e
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81510509"
---
# <a name="reduce-operator"></a>reduce 연산자

값 유사성에 따라 문자열 집합을 함께 그룹화합니다.

```kusto
T | reduce by LogMessage with threshold=0.1
```

이러한 각 그룹에 대해 그룹을 가장 잘 설명하는 **패턴(와일드카드를** 나타내는 asterix`*`(asterix) **count** 문자 사용), 그룹의 값 수 수 및 그룹의 **대표(그룹의** 원래 값 중 하나)를 출력합니다.

**구문**

*T* `|` `kind` `,` `characters` `=` *Characters* *Threshold*`with` `threshold` `=` *ReduceKind* `by` [ 감소Kind] Expr [ [ 임계값 ] [ 문자 ] *Expr* `reduce` `=`

**인수**

* *예:* 값을 평가하는 식입니다. `string`
* *임계값*: `real` 범위의 리터럴(0..1)입니다. 기본값은 0.1입니다. 큰 입력의 경우 임계값이 작아야 합니다. 
* *문자*: `string` 용어를 중단하지 않는 문자 목록에 추가할 문자 목록을 포함하는 리터럴입니다. 예를 들어, 원하는 `aaa=bbbb` 경우 `aaa:bbb` 각각 전체 용어가 될 수 `=` `:`있습니다. `":="`
* *ReduceKind*: 감소된 맛을 지정합니다. 당분간 유효한 값은 `source`.

**반환**

이 연산자는 세 개의`Pattern` `Count`열(및 `Representative`및) 행이 있는 테이블을 반환합니다. `Pattern`는 와일드카드(임의 삽입 문자열을 나타내는)로 사용되는 그룹의 `*` 패턴 값으로, `Count` 연산자에 대한 입력의 행 수가 이 `Representative` 패턴으로 표시되는 횟수를 계산하며 이 그룹에 속하는 입력의 한 값입니다.

`[kind=source]` 지정하면 연산자는 기존 테이블 `Pattern` 구조에 열을 부화시고 열을 부화시게 됩니다.
이 맛의 스키마 구문은 향후 변경될 수 있습니다.

예를 들어 `reduce by city`의 결과는 포함할 수도 있습니다. 

|패턴     |개수 |Representative|
|------------|------|--------------|
| San *      | 5182 |샌버나드   |
| Saint *    | 2846 |세인트 루시    |
| Moscow     | 3726 |Moscow        |
| \* -on- \* | 2730 |1-온-1  |
| 파리      | 2716 |파리         |

사용자 지정 토큰화의 또 다른 예는 다음과 입니다.

```kusto
range x from 1 to 1000 step 1
| project MyText = strcat("MachineLearningX", tostring(toint(rand(10))))
| reduce by MyText  with threshold=0.001 , characters = "X" 
```

|패턴         |개수|Representative   |
|----------------|-----|-----------------|
|기계 학습*|1000 |머신러닝X4|

**예**

다음 예제에서는 `reduce` 감소하기 전에 감소되는 열의 GUID를 대체하는 "위생" 입력에 연산자적용 방법을 보여 주며,

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

연산자의 `reduce` 구현은 주로 Risto Vaarandi에 의해 [이벤트 로그에서 패턴을 마이닝하기위한 데이터 클러스터링 알고리즘이라는](https://ristov.github.io/publications/slct-ipom03-web.pdf)논문을 기반으로합니다.