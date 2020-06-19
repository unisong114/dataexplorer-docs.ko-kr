---
title: search 연산자-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 검색 연산자에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: edd35e5e259666e8ce4360c072aaac6717e6f8c3
ms.sourcegitcommit: f9d3f54114fb8fab5c487b6aea9230260b85c41d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/18/2020
ms.locfileid: "85071872"
---
# <a name="search-operator"></a>search 연산자

검색 연산자는 다중 테이블/다중 열 검색 환경을 제공 합니다.

## <a name="syntax"></a>구문

* [*TabularSource* `|` ] `search`[ `kind=` *CaseSensitivity*] [ `in` `(` *TableSources* `)` ] *searchpredicate*

## <a name="arguments"></a>인수

* *TabularSource*: 테이블 이름, [union 연산자](unionoperator.md), 테이블 형식 쿼리 결과 등 검색할 데이터 원본 역할을 하는 선택적 테이블 형식 식입니다. *TableSources*를 포함 하는 선택적 구와 함께 나타날 수 없습니다.

* *CaseSensitivity*: `string` 대/소문자 구분을 기준으로 모든 스칼라 연산자의 동작을 제어 하는 선택적 플래그입니다. 유효한 값은 두 개의 동의어 `default` 이며 `case_insensitive` (예: `has` 대/소문자를 구분 하지 않는 연산자의 기본값) 및 `case_sensitive` (모든 연산자가 대/소문자를 구분 하는 일치 모드로 강제 적용)입니다.

* *TableSources*: 검색에 포함할 "와일드 카드" 테이블 이름의 선택적 쉼표로 구분 된 목록입니다.
  이 목록에는 [union 연산자](unionoperator.md)목록과 동일한 구문이 있습니다.
  선택적 *TabularSource*함께 표시할 수 없습니다.

* *Searchpredicate*: 검색할 항목을 정의 하는 필수 조건자입니다. 즉, 입력의 모든 레코드에 대해 평가 되는 부울 식이 반환 되 면 `true` 레코드가 출력 됩니다. *Searchpredicate* 구문은 부울 식에 대 한 일반 Kusto 구문을 확장 하 고 수정 합니다.

  **문자열 일치 확장명**: *searchpredicate* 에 용어로 표시 되는 문자열 리터럴은 `has` ,, `hasprefix` `hassuffix` 및 `!` 이러한 연산자의 반전 () 또는 대/소문자 구분 () 버전을 사용 하 여 모든 열과 리터럴 사이에 일치 하는 용어를 표시 `sc` 합니다. , 또는를 적용할지 ( `has` `hasprefix` `hassuffix` 또는 둘 다)를 별표 ()로 시작할지 여부에 따라 결정 `*` 됩니다. 리터럴 내부에는 별표를 사용할 수 없습니다.

    |리터럴   |연산자   |
    |----------|-----------|
    |`billg`   |`has`      |
    |`*billg`  |`hassuffix`|
    |`billg*`  |`hasprefix`|
    |`*billg*` |`contains` |
    |`bi*lg`   |`matches regex`|

  **열 제한**: 기본적으로 문자열 일치 확장 프로그램은 데이터 집합의 모든 열에 대해 일치를 시도 합니다. 다음 구문을 사용 하 여이 일치를 특정 열로 제한할 수 있습니다. *ColumnName* `:` *stringliteral*.

  **문자열 같음**: 용어 일치 대신 문자열 값과 정확히 일치 하는 열은 *ColumnName* `==` *stringliteral*구문을 사용 하 여 수행할 수 있습니다.

  **기타 부울 식**: 모든 일반 Kusto 부울 식은 구문에서 지원 됩니다.
    예를 들어은 `"error" and x==123` `error` 해당 열 중 하나에 용어를 포함 하 고 열에 값이 있는 레코드를 검색 함을 의미 `123` `x` 합니다. "

  **Regex 일치**: 정규식 일치는 *열* `matches regex` *stringliteral* 구문을 사용 하 여 표시 됩니다. 여기서 *stringliteral* 은 Regex 패턴입니다.

*TabularSource* 와 *TableSources* 를 모두 생략 하면 범위 내 데이터베이스의 모든 무제한 테이블 및 뷰에 대해 검색이 수행 됩니다.

## <a name="summary-of-string-matching-extensions"></a>문자열 일치 확장명 요약

  |# |구문                                 |의미 (해당 하는 경우 `where` )           |주석|
  |--|---------------------------------------|---------------------------------------|--------|
  | 1|`search "err"`                         |`where * has "err"`                    ||
  | 2|`search in (T1,T2,A*) and "err"`       |<code>union T1,T2,A* &#124; where * has "err"<code>   ||
  | 3|`search col:"err"`                     |`where col has "err"`                  ||
  | 4|`search col=="err"`                    |`where col=="err"`                     ||
  | 5|`search "err*"`                        |`where * hasprefix "err"`              ||
  | 6|`search "*err"`                        |`where * hassuffix "err"`              ||
  | 7|`search "*err*"`                       |`where * contains "err"`               ||
  | 8|`search "Lab*PC"`                      |`where * matches regex @"\bLab.*PC\b"`||
  | 9|`search *`                             |`where 0==0`                           ||
  |10|`search col matches regex "..."`       |`where col matches regex "..."`        ||
  |11|`search kind=case_sensitive`           |                                       |모든 문자열 비교는 대/소문자를 구분 합니다.|
  |12|`search "abc" and ("def" or "hij")`    |`where * has "abc" and (* has "def" or * has hij")`||
  |13|`search "err" or (A>a and A<b)`        |`where * has "err" or (A>a and A<b)`   ||

## <a name="remarks"></a>설명

[Find 연산자](findoperator.md) **와 달리** 연산자는 `search` 다음을 지원 하지 않습니다.

1. `withsource=`: 출력은 항상 해당 `$table` `string` 값이 각 레코드가 검색 된 테이블 이름인 형식의 이라는 열을 포함 하 고, 원본이 테이블이 아닌 복합 식일 경우 시스템에서 생성 된 이름을 포함 합니다.
2. `project=`, `project-smart` : 출력 스키마는 출력 스키마와 동일 합니다 `project-smart` .

## <a name="examples"></a>예제

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
  | 1| `search`여러 연속 연산자에 대해 단일 연산자를 사용 하는 것이 좋습니다. `search`|`search "billg" and ("steveb" or "satyan")`   |<code>search "billg" &#124; search "steveb" or "satyan"<code>           ||
  | 2| 연산자 내에서 필터링 하는 것이 좋습니다. `search`                                       |`search "billg" and "steveb"`                 |<code>search * &#124; where * has "billg" and * has "steveb"<code>      ||
