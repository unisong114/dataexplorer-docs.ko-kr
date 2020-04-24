---
title: KQL 빠른 참조
description: 유용한 KQL 함수 및 구문 예제가 있는 해당 정의 목록입니다.
author: orspod
ms.author: orspodek
ms.reviewer: ''
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/19/2020
ms.openlocfilehash: ff9b78af54141f2c7fdbbf7039aad59dca2312a0
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81500711"
---
# <a name="kql-quick-reference"></a>KQL 빠른 참조

이 문서에서는 Kusto 쿼리 언어 사용을 시작하는 데 도움이 되는 함수 목록과 설명이 표시됩니다.

| 연산자/기능                               | Description                           | 구문                                           |
| :---------------------------------------------- | :------------------------------------ |:-------------------------------------------------|
|**필터/검색/조건**                      |**_필터링 또는 검색을 통해 관련 데이터 찾기_** |                      |
| [어디](kusto/query/whereoperator.md)                      | 특정 술어의 필터           | `T | where Predicate`                         |
| [포함/있는 위치](kusto/query/whereoperator.md)        | `Contains`: 하위 문자열 일치 검색 <br> `Has`: 특정 단어(성능 향상)  | `T | where col1 contains/has "[search term]"`|
| [검색](kusto/query/searchoperator.md)                    | 테이블의 모든 열을 검색하여 값을 검색합니다. | `[TabularSource |] search [kind=CaseSensitivity] [in (TableSources)] SearchPredicate` |
| [테이크](kusto/query/takeoperator.md)                        | 지정된 레코드 수를 반환합니다. 쿼리 테스트에 사용<br>**_참고_** `_take`: `_limit`_ 및 _는 동의어입니다. | `T | take NumberOfRows` |
| [경우](kusto/query/casefunction.md)                        | 다른 시스템의 if/then elseif와 유사한 조건 문을 추가합니다. | `case(predicate_1, then_1, predicate_2, then_2, predicate_3, then_3, else)` |
| [distinct](kusto/query/distinctoperator.md)                | 입력 테이블의 제공된 열의 고유한 조합이 있는 테이블을 생성합니다. | `distinct [ColumnName], [ColumnName]` |
| **날짜/시간**                                   |**_날짜 및 시간 함수를 사용하는 작업_**               |                          |
|[전](kusto/query/agofunction.md)                           | 쿼리가 실행되는 시간을 기준으로 시간 오프셋을 반환합니다. 예를 들어 `ago(1h)` 현재 시계가 읽기 1시간 전입니다. | `ago(a_timespan)` |
| [format_datetime](kusto/query/format-datetimefunction.md)  | 다양한 날짜 형식으로 데이터를 [반환합니다.](kusto/query/format-datetimefunction.md#supported-formats) | `format_datetime(datetime , format)` |
| [빈](kusto/query/binfunction.md)                          | 타임프레임의 모든 값을 반올림하고 그룹 | `bin(value,roundTo)` |
| **열 만들기/제거**                   |**_테이블에 열 추가 또는 제거_** |                                                    |
| [인쇄](kusto/query/printoperator.md)                      | 하나 이상의 스칼라 식으로 단일 행출력 | `print [ColumnName =] ScalarExpression [',' ...]` |
| [프로젝트](kusto/query/projectoperator.md)                  | 지정된 순서에 포함할 열을 선택합니다. | `T | project ColumnName [= Expression] [, ...]` <br> Or <br> `T | project [ColumnName | (ColumnName[,]) =] Expression [, ...]` |
| [project-away](kusto/query/projectawayoperator.md)         | 출력에서 제외할 열을 선택합니다. | `T | project-away ColumnNameOrPattern [, ...]` |
| [확장](kusto/query/extendoperator.md)                    | 계산된 열을 생성하고 결과 집합에 추가합니다. | `T | extend [ColumnName | (ColumnName[, ...]) =] Expression [, ...]` |
| **데이터 집합 정렬 및 집계**                 |**_의미 있는 방식으로 데이터를 정렬하거나 그룹화하여 데이터 재구성_**|                  |
| [정렬](kusto/query/sortoperator.md)                        | 입력 테이블의 행을 오름차순 또는 내림차순으로 하나 이상의 열로 정렬합니다. | `T | sort by expression1 [asc|desc], expression2 [asc|desc], …` |
| [맨 위로](kusto/query/topoperator.md)                          | 다음을 사용하여 데이터 집합을 정렬할 때 데이터 집합의 첫 번째 N 행을 반환합니다.`by` | `T | top numberOfRows by expression [asc|desc] [nulls first|last]` |
| [요약](kusto/query/summarizeoperator.md)              | 그룹 열에 따라 `by` 행을 그룹화하고 각 그룹에 대한 집계를 계산합니다. | `T | summarize [[Column =] Aggregation [, ...]] [by [Column =] GroupExpression [, ...]]` |
| [count](kusto/query/countoperator.md)                       | 입력 테이블의 레코드 수(예: T)<br>이 연산자는`summarize count() `| `T | count` |
| [가입](kusto/query/joinoperator.md)                        | 두 테이블의 행을 병합하여 각 테이블에서 지정된 열의 값을 일치시켜 새 테이블을 형성합니다. 조인 유형의 전체 `flouter`범위를 `inner` `innerunique` `leftanti`지원합니다. `leftantisemi` `leftouter` `leftsemi` `rightanti` `rightantisemi` `rightouter``rightsemi` | `LeftTable | join [JoinParameters] ( RightTable ) on Attributes` |
| [연합](kusto/query/unionoperator.md)                      | 두 개 이상의 테이블을 가져와 모든 행을 반환합니다. | `[T1] | union [T2], [T3], …` |
| [범위](kusto/query/rangeoperator.md)                      | 산술 계열 값이 있는 테이블을 생성합니다. | `range columnName from start to stop step step` |
| **데이터 서식 지정**                                 | **_유용한 방법으로 출력하도록 데이터 재구성_** | |
| [조회](kusto/query/lookupoperator.md)                    | 치수 테이블에서 조회된 값을 사용하여 팩트 테이블의 열을 확장합니다. | `T1 | lookup [kind = (leftouter|inner)] ( T2 ) on Attributes` |
| [mv 확장](kusto/query/mvexpandoperator.md)               | 동적 배열을 행으로 변환(다중 값 확장) | `T | mv-expand Column` |
| [구문 분석](kusto/query/parseoperator.md)                      | 문자열 식을 평가하고 해당 값을 계산 열 하나 이상으로 구문 분석합니다. 비정형 데이터 구조화에 사용합니다. | `T | parse [kind=regex  [flags=regex_flags] |simple|relaxed] Expression with * (StringConstant ColumnName [: ColumnType]) *...` |
| [make-series](kusto/query/make-seriesoperator.md)          | 지정된 축을 따라 지정된 집계 값 의 계열을 만듭니다. | `T | make-series [MakeSeriesParamters] [Column =] Aggregation [default = DefaultValue] [, ...] on AxisColumn from start to end step step [by [Column =] GroupExpression [, ...]]` |
| [하자](kusto/query/letstatement.md)                         | 바인딩된 값을 참조할 수 있는 식에 이름을 바인딩합니다. 값은 쿼리의 일부로 임시 함수를 만드는 람다 식일 수 있습니다. 결과를 `let` 새 테이블처럼 보이는 테이블 위에 식을 만드는 데 사용합니다. | `let Name = ScalarExpression | TabularExpression | FunctionDefinitionExpression` |
| **일반**                                     | **_기타 작업 및 기능_** | |
| [invoke](kusto/query/invokeoperator.md)                    | 수신하는 테이블에서 함수를 입력으로 실행합니다. | `T | invoke function([param1, param2])` |
| [플러그인 이름 평가](kusto/query/evaluateoperator.md)     | 쿼리 언어 확장(플러그인) 평가 | `[T |] evaluate [ evaluateParameters ] PluginName ( [PluginArg1 [, PluginArg2]... )` |
| **시각화**                               | **_데이터를 그래픽 형식으로 표시하는 작업_** | |
| [렌더링](kusto/query/renderoperator.md) | 결과를 그래픽 출력으로 렌더링합니다. | `T | render Visualization [with (PropertyName = PropertyValue [, ...] )]` |
