---
title: Splunk to Kusto map to 데이터 탐색기 map to and Azure Monitor
description: Splunk에 대해 잘 알고 있는 사용자를 위한 개념 매핑으로, Kusto 쿼리 언어를 학습 하 여 로그 쿼리를 작성 합니다.
ms.service: data-explorer
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/21/2018
ms.openlocfilehash: 8679b47a2c698c88c4d1773f9c50dee495532ae7
ms.sourcegitcommit: faa747df81c49b96d173dbd5a28d2ca4f3a2db5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2020
ms.locfileid: "95783236"
---
# <a name="splunk-to-kusto-query-language-map"></a>Splunk to Kusto 쿼리 언어 맵

이 문서는 kusto를 사용 하 여 로그 쿼리를 작성 하는 Kusto 쿼리 언어를 Splunk에 대해 잘 알고 있는 사용자를 지원 하기 위한 것입니다. 두 항목 사이에 직접 비교가 수행 되므로 주요 차이점과 유사성을 강조 하 여 기존 정보를 바탕으로 구축할 수 있습니다.

## <a name="structure-and-concepts"></a>구조 및 개념

다음 표에서는 Splunk와 Kusto 로그 간의 개념과 데이터 구조를 비교 합니다.

 | 개념 | Splunk | Kusto |  의견 |
 |:---|:---|:---|:---|
 | 배포 단위  | cluster |  cluster |  Kusto는 임의의 클러스터 간 쿼리를 허용 합니다. Splunk는 그렇지 않습니다. |
 | 데이터 캐시 |  버킷  |  캐싱 및 보존 정책 |  데이터에 대한 기간 및 캐싱 수준을 제어합니다. 이 설정은 쿼리 성능 및 배포 비용에 직접적인 영향을 줍니다. |
 | 데이터의 논리 파티션  |  인덱스  |  데이터베이스  |  데이터를 논리적으로 분리할 수 있습니다. 두 구현 모두는 이러한 파티션에서 합집합(union)과 조인(join)을 허용합니다. |
 | 구조적 이벤트 메타 데이터 | 해당 없음 | 테이블 |  Splunk는 검색 언어에 이벤트 메타 데이터의 개념을 노출 하지 않습니다. Kusto logs에는 열을 포함 하는 테이블의 개념이 있습니다. 각 이벤트 인스턴스가 한 행에 매핑됩니다. |
 | 데이터 레코드 | event | row |  용어 변경에만 해당 |
 | 데이터 레코드 특성 | 필드(field) |  열 |  Kusto에서이 설정은 테이블 구조의 일부로 미리 정의 됩니다. Splunk에서는 각 이벤트마다 자체의 필드 집합이 있습니다. |
 | types | 데이터 형식 |  데이터 형식 |  Kusto 데이터 형식은 열에 설정 되므로 더 명시적입니다. 모두 JSON 지원을 포함 하 여 데이터 형식 및 거의 동일한 데이터 집합을 사용 하 여 동적으로 작업할 수 있습니다. |
 | 쿼리 및 검색  | search | Query |  개념은 본질적으로 Kusto와 Splunk 사이에서 동일 합니다. |
 | 이벤트 수집 시간 | 시스템 시간 | `ingestion_time()` |  Splunk에서 각 이벤트는 이벤트가 인덱싱되는 시간에 대 한 시스템 타임 스탬프를 가져옵니다. Kusto에서 [ingestion_time ()](ingestiontimefunction.md) 함수를 통해 참조할 수 있는 시스템 열을 노출 하는 [ingestion_time](../management/ingestiontimepolicy.md) 이라는 정책을 정의할 수 있습니다. |

## <a name="functions"></a>Functions

다음 표에서는 Splunk 함수에 해당 하는 Kusto의 함수를 지정 합니다.

|Splunk | Kusto |의견
|:---|:---|:---
| `strcat` | `strcat()` | (1) |
| `split`  | `split()` | (1) |
| `if`     | `iff()`   | (1) |
| `tonumber` | `todouble()`<br />`tolong()`<br />`toint()` | (1) |
| `upper`<br />`lower` |toupper()<br />`tolower()`|(1) |
| `replace` | `replace()` | (1)<br /> 또한 `replace()` 두 제품 모두에서 세 개의 매개 변수를 사용 하지만 매개 변수는 다릅니다. |
| `substr` | `substring()` | (1)<br />또한 Splunk는 1부터 시작하는 인덱스를 사용합니다. 0부터 시작 하는 인덱스를 메모 합니다. |
| `tolower` |  `tolower()` | (1) |
| `toupper` | `toupper()` | (1) |
| `match` | `matches regex` |  (2)  |
| `regex` | `matches regex` | Splunk에서 `regex`는 연산자입니다. Kusto에서 관계형 연산자입니다. |
| `searchmatch` | == | Splunk에서 `searchmatch`는 일치 문자열 검색을 허용합니다.
| `random` | rand()<br />rand(n) | Splunk의 함수는 0에서 2<sup>31</sup>-1 사이의 숫자를 반환 합니다. Kusto의은 0.0에서 1.0 사이의 숫자를 반환 하거나 매개 변수가 제공 된 경우 0과 n-1 사이에 있는 숫자를 반환 합니다.
| `now` | `now()` | (1)
| `relative_time` | `totimespan()` | (1)<br />Kusto에서에 해당 하는 Splunk `relative_time(datetimeVal, offsetVal)` 은입니다 `datetimeVal + totimespan(offsetVal)` .<br />예를 들어 `search` &#124; `eval n=relative_time(now(), "-1d@d")` `...`  &#124; 됩니다 `extend myTime = now() - totimespan("1d")` .

(1) Splunk에서는 연산자를 사용 하 여 함수를 호출 합니다 `eval` . Kusto에서 또는의 일부로 사용 `extend` `project` 됩니다.<br />(2) Splunk에서는 연산자를 사용 하 여 함수를 호출 합니다 `eval` . Kusto에서 연산자와 함께 사용할 수 있습니다 `where` .


## <a name="operators"></a>연산자

다음 섹션에서는 Splunk 및 Kusto에서 다른 연산자를 사용 하는 방법에 대 한 예제를 제공 합니다.

> [!NOTE]
> 다음 예에서는 Splunk 필드가 `rule` Kusto의 테이블에 매핑되고 Splunk의 기본 타임 스탬프가 Logs Analytics 열에 매핑됩니다 `ingestion_time()` .

### <a name="search"></a>검색

Splunk에서는 `search` 키워드를 생략하고 따옴표가 없는 문자열을 지정할 수 있습니다. Kusto에서를 사용 하 여 각 쿼리를 시작 해야 하 `find` 고, 따옴표 붙지 않은 문자열이 열 이름 이며, 조회 값은 따옴표 붙은 문자열 이어야 합니다. 

| 제품 | 연산자 | 예제 |
|:---|:---|:---|
| Splunk | `search` | `search Session.Id="c8894ffd-e684-43c9-9125-42adc25cd3fc" earliest=-24h` |
| Kusto | `find` | `find Session.Id=="c8894ffd-e684-43c9-9125-42adc25cd3fc" and ingestion_time()> ago(24h)` |


### <a name="filter"></a>Assert

Kusto 로그 쿼리는가 적용 되는 테이블 형식 결과 집합에서 시작 됩니다 `filter` . Splunk에서 필터링은 현재 인덱스에 대한 기본 작업입니다. `where`Splunk에서 연산자를 사용할 수도 있지만 권장 되지는 않습니다.

| 제품 | 연산자 | 예제 |
|:---|:---|:---|
| Splunk | `search` | `Event.Rule="330009.2" Session.Id="c8894ffd-e684-43c9-9125-42adc25cd3fc" _indextime>-24h` |
| Kusto | `where` | `Office_Hub_OHubBGTaskError`<br />&#124; `where Session_Id == "c8894ffd-e684-43c9-9125-42adc25cd3fc" and ingestion_time() > ago(24h)` |

### <a name="get-n-events-or-rows-for-inspection"></a>검사를 위해 *n* 개의 이벤트 또는 행 가져오기

Kusto 로그 쿼리 `take` 는에 대 한 별칭으로도 지원 `limit` 합니다. Splunk에서 결과가 정렬 되는 경우는 `head` 처음 *n 개* 결과를 반환 합니다. Kusto에서는 `limit` 순서가 지정 되지 않지만 발견 된 처음 *n 개* 행을 반환 합니다.

| 제품 | 연산자 | 예제 |
|:---|:---|:---|
| Splunk | `head` | `Event.Rule=330009.2`<br />&#124; `head 100` |
| Kusto | `limit` | `Office_Hub_OHubBGTaskError`<br />&#124; `limit 100` |

### <a name="get-the-first-n-events-or-rows-ordered-by-a-field-or-column"></a>필드 또는 열을 기준으로 정렬 된 처음 *n 개* 이벤트 또는 행 가져오기

하위 결과에 대해서는 Splunk에서를 사용 `tail` 합니다. Kusto에서를 사용 하 여 순서 방향을 지정할 수 있습니다 `asc` .

| 제품 | 연산자 | 예제 |
|:---|:---|:---|
| Splunk | `head` |  `Event.Rule="330009.2"`<br />&#124; `sort Event.Sequence`<br />&#124; `head 20` |
| Kusto | `top` | `Office_Hub_OHubBGTaskError`<br />&#124; `top 20 by Event_Sequence` |

### <a name="extend-the-result-set-with-new-fields-or-columns"></a>새 필드 또는 열을 사용 하 여 결과 집합 확장

Splunk에는 `eval` 함수가 있지만 `eval` kusto의 연산자와 비교할 수 없습니다. `eval`Splunk의 연산자와 `extend` kusto 연산자 모두 스칼라 함수 및 산술 연산자를 지원 합니다.

| 제품 | 연산자 | 예제 |
|:---|:---|:---|
| Splunk | `eval` |  `Event.Rule=330009.2`<br />&#124; `eval state= if(Data.Exception = "0", "success", "error")` |
| Kusto | `extend` | `Office_Hub_OHubBGTaskError`<br />&#124; `extend state = iif(Data_Exception == 0,"success" ,"error")` |

### <a name="rename"></a>이름 바꾸기

Kusto는 연산자를 사용 하 여 `project-rename` 필드 이름을 바꿉니다. 연산자에서 `project-rename` 쿼리는 필드에 대해 미리 작성 된 인덱스를 사용할 수 있습니다. Splunk에는 `rename` 동일 하 게 작동 하는 연산자가 있습니다.

| 제품 | 연산자 | 예제 |
|:---|:---|:---|
| Splunk | `rename` |  `Event.Rule=330009.2`<br />&#124; `rename Date.Exception as execption` |
| Kusto | `project-rename` | `Office_Hub_OHubBGTaskError`<br />&#124; `project-rename exception = Date_Exception` |

### <a name="format-results-and-projection"></a>결과 및 프로젝션 서식 지정

Splunk에는와 유사한 연산자가 없습니다 `project-away` . UI를 사용 하 여 필드를 필터링 할 수 있습니다.

| 제품 | 연산자 | 예제 |
|:---|:---|:---|
| Splunk | `table` |  `Event.Rule=330009.2`<br />&#124; `table rule, state` |
| Kusto | `project`<br />`project-away` | `Office_Hub_OHubBGTaskError`<br />&#124; `project exception, state` |

### <a name="aggregation"></a>집계

사용 가능한 [집계 함수 목록을](summarizeoperator.md#list-of-aggregation-functions) 참조 하십시오.

| 제품 | 연산자 | 예제 |
|:---|:---|:---|
| Splunk | `stats` |  `search (Rule=120502.*)`<br />&#124; `stats count by OSEnv, Audience` |
| Kusto | `summarize` | `Office_Hub_OHubBGTaskError`<br />&#124; `summarize count() by App_Platform, Release_Audience` |


### <a name="join"></a>Join

`join` Splunk에는 상당한 제한이 있습니다. 하위 쿼리는 1만 개 결과 (배포 구성 파일에 설정)를 제한 하 고 제한 된 수의 조인 특성을 사용할 수 있습니다.

| 제품 | 연산자 | 예제 |
|:---|:---|:---|
| Splunk | `join` |  `Event.Rule=120103* &#124; stats by Client.Id, Data.Alias` <br />&#124; `join Client.Id max=0 [search earliest=-24h Event.Rule="150310.0" Data.Hresult=-2147221040]` |
| Kusto | `join` | `cluster("OAriaPPT").database("Office PowerPoint").Office_PowerPoint_PPT_Exceptions`<br />&#124; `where  Data_Hresult== -2147221040`<br />&#124; `join kind = inner (Office_System_SystemHealthMetadata`<br />&#124; `summarize by Client_Id, Data_Alias)on Client_Id`   |

### <a name="sort"></a>정렬

Splunk에서 오름차순으로 정렬 하려면 연산자를 사용 해야 합니다 `reverse` . Kusto는 시작 이나 끝에서 null을 배치할 위치를 정의 하는 기능도 지원 합니다.

| 제품 | 연산자 | 예제 |
|:---|:---|:---|
| Splunk | `sort` |  `Event.Rule=120103`<br />&#124; `sort Data.Hresult` <br />&#124; `reverse` |
| Kusto | `order by` | `Office_Hub_OHubBGTaskError`<br />&#124; `order by Data_Hresult,  desc` |

### <a name="multivalue-expand"></a>다중값 확장

다중값 expand 연산자는 Splunk와 Kusto 사이에서 비슷합니다.

| 제품 | 연산자 | 예제 |
|:---|:---|:---|
| Splunk | `mvexpand` |  `mvexpand solutions` |
| Kusto | `mv-expand` | `mv-expand solutions` |

### <a name="result-facets-interesting-fields"></a>결과 패싯, 흥미로운 필드

Azure Portal의 Log Analytics에서는 첫 번째 열만 공개됩니다. 모든 열은 API를 통해 사용할 수 있습니다.

| 제품 | 연산자 | 예제 |
|:---|:---|:---|
| Splunk | `fields` |  `Event.Rule=330009.2`<br />&#124; `fields App.Version, App.Platform` |
| Kusto | `facets` | `Office_Excel_BI_PivotTableCreate`<br />&#124; `facet by App_Branch, App_Version` |

### <a name="deduplicate"></a>중복 제거

Kusto에서를 사용 하 여 `summarize arg_min()` 레코드를 선택 하는 순서를 바꿀 수 있습니다.

| 제품 | 연산자 | 예제 |
|:---|:---|:---|
| Splunk | `dedup` |  `Event.Rule=330009.2`<br />&#124; `dedup device_id sortby -batterylife` |
| Kusto | `summarize arg_max()` | `Office_Excel_BI_PivotTableCreate`<br />&#124; `summarize arg_max(batterylife, *) by device_id` |

## <a name="next-steps"></a>다음 단계

- [Kusto 쿼리 언어](tutorial.md?pivots=azuremonitor)에 대 한 자습서를 안내 합니다.
