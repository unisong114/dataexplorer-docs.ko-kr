---
title: KQL 빠른 참조
description: 구문 예제가 포함 된 유용한 KQL 함수 및 해당 정의의 목록입니다.
author: orspod
ms.author: orspodek
ms.reviewer: ''
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/19/2020
ms.openlocfilehash: 2fa4cbd0b1cf7b034bc7ae3202afcde3866ca347
ms.sourcegitcommit: ee904f45e3eb3feab046263aa9956cb7780a056d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92356591"
---
# <a name="kql-quick-reference"></a>KQL 빠른 참조

이 문서에서는 Kusto 쿼리 언어를 사용 하 여 시작 하는 데 도움이 되는 함수 목록과 해당 설명을 보여 줍니다.

| Operator/함수                               | 설명                           | 구문                                           |
| :---------------------------------------------- | :------------------------------------ |:-------------------------------------------------|
|**필터/검색/조건**                      |**_필터링 또는 검색을 통해 관련 데이터 찾기_** |                      |
| [where](kusto/query/whereoperator.md)                      | 특정 조건자의 필터           | `T | where Predicate`                         |
| [여기에는/가 포함 됩니다.](kusto/query/whereoperator.md)        | `Contains`: 부분 문자열 일치 항목을 찾습니다. <br> `Has`: 특정 단어를 찾습니다 (성능 향상).  | `T | where col1 contains/has "[search term]"`|
| [search](kusto/query/searchoperator.md)                    | 테이블에서 값에 대 한 모든 열을 검색 합니다. | `[TabularSource |] search [kind=CaseSensitivity] [in (TableSources)] SearchPredicate` |
| [take](kusto/query/takeoperator.md)                        | 지정 된 수의 레코드를 반환 합니다. 쿼리를 테스트 하는 데 사용<br>**_참고_**: `_take` _ 및 `_limit` _는 동의어입니다. | `T | take NumberOfRows` |
| [case](kusto/query/casefunction.md)                        | 다른 시스템에서 if/then/elseif와 유사한 조건문을 추가 합니다. | `case(predicate_1, then_1, predicate_2, then_2, predicate_3, then_3, else)` |
| [distinct](kusto/query/distinctoperator.md)                | 입력 테이블의 제공된 열의 고유한 조합으로 테이블을 생성합니다. | `distinct [ColumnName], [ColumnName]` |
| **날짜/시간**                                   |**_날짜 및 시간 함수를 사용 하는 작업_**               |                          |
|[전보다](kusto/query/agofunction.md)                           | 쿼리가 실행 되는 시간에 상대적인 시간 오프셋을 반환 합니다. 예를 들어 `ago(1h)` 는 현재 clock의 읽기 전에 1 시간입니다. | `ago(a_timespan)` |
| [format_datetime](kusto/query/format-datetimefunction.md)  | [다양 한 날짜 형식](kusto/query/format-datetimefunction.md#supported-formats)으로 데이터를 반환 합니다. | `format_datetime(datetime , format)` |
| [저장소](kusto/query/binfunction.md)                          | 시간대의 모든 값을 반올림 하 고 그룹화 합니다. | `bin(value,roundTo)` |
| **열 만들기/제거**                   |**_테이블에서 열 추가 또는 제거_** |                                                    |
| [print](kusto/query/printoperator.md)                      | 하나 이상의 스칼라 식이 포함 된 단일 행을 출력 합니다. | `print [ColumnName =] ScalarExpression [',' ...]` |
| [project](kusto/query/projectoperator.md)                  | 지정 된 순서에 포함할 열을 선택 합니다. | `T | project ColumnName [= Expression] [, ...]` <br> 또는 <br> `T | project [ColumnName | (ColumnName[,]) =] Expression [, ...]` |
| [project-away](kusto/query/projectawayoperator.md)         | 출력에서 제외할 열을 선택 합니다. | `T | project-away ColumnNameOrPattern [, ...]` |
| [프로젝트-유지](kusto/query/project-keep-operator.md)         | 출력에 유지할 열을 선택 합니다. | `T | project-keep ColumnNameOrPattern [, ...]` |
| [프로젝트 이름 바꾸기](kusto/query/projectrenameoperator.md)     | 결과 출력에서 열 이름 바꾸기 | `T | project-rename new_column_name = column_name` |
| [프로젝트-순서 변경](kusto/query/projectreorderoperator.md)   | 결과 출력의 열 순서 다시 정렬 | `T | project-reorder Col2, Col1, Col* asc` |
| [extend](kusto/query/extendoperator.md)                    | 계산 열을 만들어 결과 집합에 추가 합니다. | `T | extend [ColumnName | (ColumnName[, ...]) =] Expression [, ...]` |
| **데이터 집합 정렬 및 집계**                 |**_의미 있는 방식으로 정렬 하거나 그룹화 하 여 데이터 재구성_**|                  |
| [sort](kusto/query/sortoperator.md)                        | 입력 테이블의 행을 하나 이상의 열을 기준으로 오름차순 또는 내림차순으로 정렬 합니다. | `T | sort by expression1 [asc|desc], expression2 [asc|desc], …` |
| [top](kusto/query/topoperator.md)                          | 를 사용 하 여 데이터 집합을 정렬할 때 데이터 집합의 처음 N 개 행을 반환 합니다. `by` | `T | top numberOfRows by expression [asc|desc] [nulls first|last]` |
| [summarize](kusto/query/summarizeoperator.md)              | 그룹 열에 따라 행을 그룹화 `by` 하 고 각 그룹에 대 한 집계를 계산 합니다. | `T | summarize [[Column =] Aggregation [, ...]] [by [Column =] GroupExpression [, ...]]` |
| [count](kusto/query/countoperator.md)                       | 입력 테이블의 레코드 수를 계산 합니다 (예: T).<br>이 연산자는의 축약형입니다. `summarize count() `| `T | count` |
| [조인](kusto/query/joinoperator.md)                        | 각 테이블의 지정 된 열 값을 일치 시켜 새 테이블을 만드는 두 테이블의 행을 병합 합니다. 는,,,,,,,,, `flouter` `inner` `innerunique` `leftanti` `leftantisemi` `leftouter` `leftsemi` `rightanti` `rightantisemi` `rightouter` , 등의 모든 범위의 조인 형식을 지원 합니다. `rightsemi` | `LeftTable | join [JoinParameters] ( RightTable ) on Attributes` |
| [union](kusto/query/unionoperator.md)                      | 두 개 이상의 테이블을 사용 하 고 모든 행을 반환 합니다. | `[T1] | union [T2], [T3], …` |
| [range](kusto/query/rangeoperator.md)                      | 산술 계열의 값이 있는 테이블을 생성 합니다. | `range columnName from start to stop step step` |
| **데이터 서식 지정**                                 | **_유용한 방식으로 데이터를 출력으로 재구성_** | |
| [조회](kusto/query/lookupoperator.md)                    | 팩트 테이블의 열을 차원 테이블에서 조회 한 값으로 확장 합니다. | `T1 | lookup [kind = (leftouter|inner)] ( T2 ) on Attributes` |
| [mv-expand](kusto/query/mvexpandoperator.md)               | 동적 배열을 행으로 변환 합니다 (다중 값 확장). | `T | mv-expand Column` |
| [구문 분석](kusto/query/parseoperator.md)                      | 문자열 식을 평가하고 해당 값을 계산 열 하나 이상으로 구문 분석합니다. 비구조적 데이터를 구조화 하는 데 사용 합니다. | `T | parse [kind=regex  [flags=regex_flags] |simple|relaxed] Expression with * (StringConstant ColumnName [: ColumnType]) *...` |
| [make-series](kusto/query/make-seriesoperator.md)          | 지정 된 축을 따라 지정 된 일련의 집계 된 값을 만듭니다. | `T | make-series [MakeSeriesParamters] [Column =] Aggregation [default = DefaultValue] [, ...] on AxisColumn from start to end step step [by [Column =] GroupExpression [, ...]]` |
| [let](kusto/query/letstatement.md)                         | 바인딩된 값을 참조할 수 있는 식에 이름을 바인딩합니다. 값은 쿼리의 일부로 임시 함수를 만드는 람다 식일 수 있습니다. `let`를 사용 하 여 결과를 새 테이블 처럼 표시 하는 테이블에 대 한 식을 만듭니다. | `let Name = ScalarExpression | TabularExpression | FunctionDefinitionExpression` |
| **일반**                                     | **_기타 작업 및 함수_** | |
| [invoke](kusto/query/invokeoperator.md)                    | 입력으로 수신 되는 테이블에서 함수를 실행 합니다. | `T | invoke function([param1, param2])` |
| [pluginName 평가](kusto/query/evaluateoperator.md)     | 쿼리 언어 확장 (플러그 인)을 평가 합니다. | `[T |] evaluate [ evaluateParameters ] PluginName ( [PluginArg1 [, PluginArg2]... )` |
| **시각화**                               | **_데이터를 그래픽 형식으로 표시 하는 작업_** | |
| [렌더](kusto/query/renderoperator.md) | 결과를 그래픽 출력으로 렌더링 합니다. | `T | render Visualization [with (PropertyName = PropertyValue [, ...] )]` |
