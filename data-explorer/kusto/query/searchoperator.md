---
title: 검색 연산자 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 검색 연산자에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: de63aa3fde421996809334b8a746ea4dee8cb026
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81509200"
---
# <a name="search-operator"></a>search 연산자

검색 연산자는 다중 테이블/다중 열 검색 환경을 제공합니다.

## <a name="syntax"></a>구문

* [*표출처* `|`] `search` [`kind=`*케이스 감도*] [`in` `(` *[테이블 소스*`)`] *검색*

## <a name="arguments"></a>인수

* *TabularSource*: 테이블 이름, [공용 구조체 연산자,](unionoperator.md)테이블 형식 쿼리 결과 등과 같이 검색할 데이터 원본 역할을 하는 선택적 테이블 형식 식입니다. *TableSource*를 포함하는 선택적 구와 함께 나타날 수 없습니다.

* *CaseSensitivity*: 대/소문자 민감도와 `string` 관련하여 모든 스칼라 연산자의 동작을 제어하는 선택적 플래그입니다. 유효한 값은 두 `default` 동의어와 `case_insensitive` `has`(즉, 대/소문자를 구분하지 않는 연산자의 기본값) 및 `case_sensitive` (이러한 모든 연산자가 대/소문자를 구분하는 일치 모드로 강제 변환됨)입니다.

* *TableSources:* 검색에 참여할 "와일드카드" 테이블 이름의 선택적 쉼표 분리 목록입니다.
  목록에는 [공용 구조자 연산자](unionoperator.md)목록과 동일한 구문이 있습니다.
  선택적 *TabularSource*와 함께 나타날 수 없습니다.

* *SearchPredicate*: 검색 할 내용을 정의하는 필수 술어 (즉, 입력의 모든 레코드에 대해 평가되고 반환하는 `true`경우 레코드가 출력되는 부울 식)입니다. *SearchPredicate에* 대한 구문은 부울 식에 대한 일반 Kusto 구문을 확장하고 수정합니다.

  **문자열 일치 확장**: *SearchPredicate에서* 용어로 나타나는 문자열 리터럴은 모든 열과 리터럴 `has` `hasprefix`을 `hassuffix`사용하여 용어 일치를`!`나타냅니다 .`sc` 을 `has` `hasprefix`적용할지 아니면 별표()에 `hassuffix` `*`의해 리터럴이 시작되거나 종료되는지(또는 둘 다)에 따라 결정됩니다. 리터럴 내부의 별표는 허용되지 않습니다.

    |리터럴   |연산자   |
    |----------|-----------|
    |`billg`   |`has`      |
    |`*billg`  |`hassuffix`|
    |`billg*`  |`hasprefix`|
    |`*billg*` |`contains` |
    |`bi*lg`   |`matches regex`|

  **열 제한**: 기본적으로 문자열 일치 확장은 데이터 집합의 모든 열과 일치하려고 시도합니다. 다음 구문인 *ColumnName*`:`*StringLiteral을*사용하여 이 일치를 특정 열로 제한할 수 있습니다.

  **문자열 같음**: 문자열 값에 대한 열의 정확한 일치 (용어 일치 대신)는 구문 *ColumnName*`==`*StringLiteral을*사용하여 수행 할 수 있습니다.

  **기타 부울 식**: 모든 일반 Kusto Boolean 표현식은 구문에서 지원됩니다.
    예를 `"error" and x==123` 들어, 열에 용어가 `error` 있고 `123` `x` 열에 값이 있는 레코드를 검색하는 것을 의미합니다."

  **정규식 일치**: 정규식 일치는 *StringLiteral이* 정규법 패턴인 *열* `matches regex` *StringLiteral* 구문을 사용하여 표시됩니다.

*TabularSource와* *TableSource를* 모두 생략하면 검색이 제한되지 않은 모든 테이블과 범위내 데이터베이스 뷰를 통해 수행됩니다.

## <a name="summary-of-string-matching-extensions"></a>문자열 일치 확장 요약

  |# |구문                                 |의미(해당) `where`           |주석|
  |--|---------------------------------------|---------------------------------------|--------|
  | 1|`search "err"`                         |`where * has "err"`                    ||
  | 2|`search in (T1,T2,A*) and "err"`       |<code>union T1,T2,A* &#124; where * has "err"<code>   ||
  | 3|`search col:"err"`                     |`where col has "err"`                  ||
  | 4|`search col=="err"`                    |`where col=="err"`                     ||
  | 5|`search "err*"`                        |`where * hasprefix "err"`              ||
  | 6|`search "*err"`                        |`where * hassuffix "err"`              ||
  | 7|`search "*err*"`                       |`where * contains "err"`               ||
  | 8|`search "Lab*PC"`                      |`where * matches regex @"\bLab\w*PC\b"`||
  | 9|`search *`                             |`where 0==0`                           ||
  |10|`search col matches regex "..."`       |`where col matches regex "..."`        ||
  |11|`search kind=case_sensitive`           |                                       |모든 문자열 비교는 대/소문자를 구분합니다.|
  |12|`search "abc" and ("def" or "hij")`    |`where * has "abc" and (* has "def" or * has hij")`||
  |13|`search "err" or (A>a and A<b)`        |`where * has "err" or (A>a and A<b)`   ||

## <a name="remarks"></a>설명

찾기 [연산자와](findoperator.md) `search` **달리** 연산자는 다음을 지원하지 않습니다.

1. `withsource=`: 출력에는 항상 값이 `$table` 각 `string` 레코드가 검색된 테이블 이름인 형식의 열(또는 소스가 테이블이 아니라 복합 식인 경우 일부 시스템 생성 이름)이 포함됩니다.
2. `project=`: `project-smart`출력 스키마는 출력 `project-smart` 스키마와 동일합니다.

## <a name="examples"></a>예

```kusto
// 1. Simple term search over all unrestricted tables and views of the database in scope
search "billg"

// 2. Like (1), but looking only for records that match both terms
search "billg" and ("steveb" or "satyan")

// 3. Like (1), but looking only in the TraceEvent table
search in (TraceEvent) and "billg"

// 4. Like (2), but performing a case-sensitive match of all terms
search "BillB" and ("SteveB" or "SatyaN")

// 5. Like (1), but restricting the match to some columns
search CEO:"billg" or CSA:"billg"

// 6. Like (1), but only for some specific time limit
search "billg" and Timestamp >= datetime(1981-01-01)

// 7. Searches over all the higher-ups
search in (C*, TF) "billg" or "davec" or "steveb"

// 8. A different way to say (7). Prefer to use (7) when possible
union C*, TF | search "billg" or "davec" or "steveb"
```

## <a name="performance-tips"></a>성능 팁

  |# |팁                                                                                  |Prefer                                        |위                                                                    |
  |--|-------------------------------------------------------------------------------------|----------------------------------------------|------------------------------------------------------------------------|
  | 1| 여러 연속 `search` `search` 연산자보다 단일 연산자 사용 선호|`search "billg" and ("steveb" or "satyan")`   |<code>search "billg" &#124; search "steveb" or "satyan"<code>           ||
  | 2| 작업자 내부를 `search` 필터링하는 것을 선호합니다.                                       |`search "billg" and "steveb"`                 |<code>search * &#124; where * has "billg" and * has "steveb"<code>      ||
