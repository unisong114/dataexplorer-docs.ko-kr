---
title: KQL 빠른 참조
description: 구문 예제가 포함된 유용한 KQL 함수 및 해당 정의의 목록입니다.
author: orspod
ms.author: orspodek
ms.reviewer: ''
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/19/2020
ms.localizationpriority: high
ms.openlocfilehash: 3b007d1688130449c597ef99281ed89b55d880eb
ms.sourcegitcommit: f49e581d9156e57459bc69c94838d886c166449e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2020
ms.locfileid: "95511927"
---
# <a name="kql-quick-reference"></a>KQL 빠른 참조

이 문서에서는 Kusto 쿼리 언어를 사용하는 데 도움이 되는 함수 목록 및 해당 설명을 보여 줍니다.

| 연산자/함수                               | 설명                           | Syntax                                           |
| :---------------------------------------------- | :------------------------------------ |:-------------------------------------------------|
|**필터링/검색/조건**                      |**_필터링 또는 검색을 통해 관련 데이터 찾기_** |                      |
| [where](kusto/query/whereoperator.md)                      | 특정 조건자를 필터링합니다.           | `T | where Predicate`                         |
| [where contains/has](kusto/query/whereoperator.md)        | `Contains`: 부분 문자열 일치 항목을 찾습니다. <br> `Has`: 특정 단어를 찾습니다(성능 향상).  | `T | where col1 contains/has "[search term]"`|
| [search](kusto/query/searchoperator.md)                    | 테이블의 모든 열에서 값을 검색합니다. | `[TabularSource |] search [kind=CaseSensitivity] [in (TableSources)] SearchPredicate` |
| [take](kusto/query/takeoperator.md)                        | 지정된 레코드 수를 반환합니다. 쿼리를 테스트하는 데 사용합니다.<br>**_참고_**: `_take`_ 및 `_limit`_는 동의어입니다. | `T | take NumberOfRows` |
| [case](kusto/query/casefunction.md)                        | 다른 시스템의 if/then/elseif와 비슷한 조건 문을 추가합니다. | `case(predicate_1, then_1, predicate_2, then_2, predicate_3, then_3, else)` |
| [distinct](kusto/query/distinctoperator.md)                | 입력 테이블의 제공된 열의 고유한 조합으로 테이블을 생성합니다. | `distinct [ColumnName], [ColumnName]` |
| **날짜/시간**                                   |**_날짜 및 시간 함수를 사용하는 작업_**               |                          |
|[ago](kusto/query/agofunction.md)                           | 쿼리가 실행되는 시간을 기준으로 하는 상대적인 시간 오프셋을 반환합니다. 예를 들어 `ago(1h)`는 현재 시계의 판독 값 이전의 1시간입니다. | `ago(a_timespan)` |
| [format_datetime](kusto/query/format-datetimefunction.md)  | [다양한 날짜 형식](kusto/query/format-datetimefunction.md#supported-formats)의 데이터를 반환합니다. | `format_datetime(datetime , format)` |
| [bin](kusto/query/binfunction.md)                          | 시간 범위의 모든 값을 반올림하고 그룹화합니다. | `bin(value,roundTo)` |
| **열 만들기/제거**                   |**_테이블에서 열 추가 또는 제거_** |                                                    |
| [print](kusto/query/printoperator.md)                      | 하나 이상의 스칼라 식이 포함된 단일 행을 출력합니다. | `print [ColumnName =] ScalarExpression [',' ...]` |
| [project](kusto/query/projectoperator.md)                  | 지정된 순서대로 포함할 열을 선택합니다. | `T | project ColumnName [= Expression] [, ...]` <br> 또는 <br> `T | project [ColumnName | (ColumnName[,]) =] Expression [, ...]` |
| [project-away](kusto/query/projectawayoperator.md)         | 출력에서 제외할 열을 선택합니다. | `T | project-away ColumnNameOrPattern [, ...]` |
| [project-keep](kusto/query/project-keep-operator.md)         | 출력에서 유지할 열을 선택합니다. | `T | project-keep ColumnNameOrPattern [, ...]` |
| [project-rename](kusto/query/projectrenameoperator.md)     | 결과 출력에서 열 이름을 바꿉니다. | `T | project-rename new_column_name = column_name` |
| [project-reorder](kusto/query/projectreorderoperator.md)   | 결과 출력에서 열 순서를 다시 정렬합니다. | `T | project-reorder Col2, Col1, Col* asc` |
| [extend](kusto/query/extendoperator.md)                    | 계산 열을 만들어 결과 집합에 추가합니다. | `T | extend [ColumnName | (ColumnName[, ...]) =] Expression [, ...]` |
| **데이터 세트 정렬 및 집계**                 |**_데이터를 의미 있는 방식으로 정렬하거나 그룹화하여 데이터 재구성_**|                  |
| [sort](kusto/query/sortoperator.md)                        | 하나 이상의 열을 기준으로 입력 테이블 행을 오름차순 또는 내림차순으로 정렬합니다. | `T | sort by expression1 [asc|desc], expression2 [asc|desc], …` |
| [top](kusto/query/topoperator.md)                          | `by`를 사용하여 데이터 세트를 정렬할 때 데이터 세트의 처음 N개 행을 반환합니다. | `T | top numberOfRows by expression [asc|desc] [nulls first|last]` |
| [summarize](kusto/query/summarizeoperator.md)              | `by` 그룹 열에 따라 행을 그룹화하고, 각 그룹에 대한 집계를 계산합니다. | `T | summarize [[Column =] Aggregation [, ...]] [by [Column =] GroupExpression [, ...]]` |
| [count](kusto/query/countoperator.md)                       | 입력 테이블의 레코드 수를 계산합니다(예: T).<br>이 연산자는 `summarize count() `의 축약형입니다.| `T | count` |
| [join](kusto/query/joinoperator.md)                        | 두 테이블의 행을 병합하여 각 테이블에서 지정된 열의 값을 일치시키는 방식으로 새 테이블을 만듭니다. 지원되는 모든 join 형식은 `flouter`, `inner`, `innerunique`, `leftanti`, `leftantisemi`, `leftouter`, `leftsemi`, `rightanti`, `rightantisemi`, `rightouter`, `rightsemi`입니다. | `LeftTable | join [JoinParameters] ( RightTable ) on Attributes` |
| [union](kusto/query/unionoperator.md)                      | 둘 이상의 테이블을 사용하고 해당 행을 모두 반환합니다. | `[T1] | union [T2], [T3], …` |
| [range](kusto/query/rangeoperator.md)                      | 산술 계열의 값이 포함된 테이블을 생성합니다. | `range columnName from start to stop step step` |
| **데이터 서식 지정**                                 | **_데이터를 유용한 방식으로 출력하도록 재구성_** | |
| [lookup](kusto/query/lookupoperator.md)                    | 차원 테이블에서 조회된 값을 사용하여 팩트 테이블의 열을 확장합니다. | `T1 | lookup [kind = (leftouter|inner)] ( T2 ) on Attributes` |
| [mv-expand](kusto/query/mvexpandoperator.md)               | 동적 배열을 행(다중 값 확장)으로 변환합니다. | `T | mv-expand Column` |
| [parse](kusto/query/parseoperator.md)                      | 문자열 식을 평가하고 해당 값을 계산 열 하나 이상으로 구문 분석합니다. 비정형 데이터를 구조화하는 데 사용합니다. | `T | parse [kind=regex  [flags=regex_flags] |simple|relaxed] Expression with * (StringConstant ColumnName [: ColumnType]) *...` |
| [make-series](kusto/query/make-seriesoperator.md)          | 지정된 축을 따라 지정된 집계 값 계열을 만듭니다. | `T | make-series [MakeSeriesParamters] [Column =] Aggregation [default = DefaultValue] [, ...] on AxisColumn from start to end step step [by [Column =] GroupExpression [, ...]]` |
| [let](kusto/query/letstatement.md)                         | 바인딩된 값을 참조할 수 있는 식에 이름을 바인딩합니다. 값은 쿼리의 일부로 임시 함수를 만드는 람다 식일 수 있습니다. `let`을 사용하여 결과가 새 테이블처럼 표시되는 테이블에 대한 식을 만듭니다. | `let Name = ScalarExpression | TabularExpression | FunctionDefinitionExpression` |
| **일반**                                     | **_기타 작업 및 함수_** | |
| [invoke](kusto/query/invokeoperator.md)                    | 입력으로 받는 테이블에서 함수를 실행합니다. | `T | invoke function([param1, param2])` |
| [evaluate pluginName](kusto/query/evaluateoperator.md)     | 쿼리 언어 확장(플러그 인)을 평가합니다. | `[T |] evaluate [ evaluateParameters ] PluginName ( [PluginArg1 [, PluginArg2]... )` |
| **시각화**                               | **_데이터를 그래픽 형식으로 표시하는 작업_** | |
| [render](kusto/query/renderoperator.md) | 결과를 그래픽 출력으로 렌더링합니다. | `T | render Visualization [with (PropertyName = PropertyValue [, ...] )]` |
