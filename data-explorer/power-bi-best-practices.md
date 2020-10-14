---
title: Power BI를 사용 하 여 Azure 데이터 탐색기 데이터를 쿼리하고 시각화 하는 방법에 대 한 모범 사례
description: 이 문서에서는 Power BI를 사용 하 여 Azure 데이터 탐색기 데이터를 쿼리하고 시각화 하는 모범 사례를 알아봅니다.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: how-to
ms.date: 09/26/2019
ms.openlocfilehash: 4cb2c64e2a66d8412277717e505626965bca1052
ms.sourcegitcommit: 7024f73c76bf5b506557fd0ef7a0f4f40ec7d313
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92021222"
---
# <a name="best-practices-for-using-power-bi-to-query-and-visualize-azure-data-explorer-data"></a>Power BI를 사용 하 여 Azure 데이터 탐색기 데이터를 쿼리하고 시각화 하는 방법에 대 한 모범 사례

Azure 데이터 탐색기는 로그 및 원격 분석 데이터에 사용 가능한 빠르고 확장성이 우수한 데이터 탐색 서비스입니다. [Power BI](https://docs.microsoft.com/power-bi/) 은 데이터를 시각화 하 고 조직 전체에서 결과를 공유할 수 있는 비즈니스 분석 솔루션입니다. Azure 데이터 탐색기는 Power BI 데이터에 연결 하기 위한 세 가지 옵션을 제공 합니다. [기본 제공 커넥터](power-bi-connector.md)를 사용 하 여 [Azure 데이터 탐색기에서 Power BI로 쿼리를 가져오거나](power-bi-imported-query.md) [SQL 쿼리](power-bi-sql-query.md)를 사용 합니다. 이 문서에서는 Power BI를 사용 하 여 Azure 데이터 탐색기 데이터를 쿼리하고 시각화 하기 위한 팁을 제공 합니다. 

## <a name="best-practices-for-using-power-bi"></a>Power BI 사용에 대 한 모범 사례

Tb의 새로운 원시 데이터로 작업 하는 경우 다음 지침에 따라 Power BI 대시보드 및 보고서를 계속 해 서 snappy 하 고 업데이트 합니다.

* **여행 광원** -보고서를 Power BI 하는 데 필요한 데이터만 가져옵니다. 심층 대화형 분석을 위해 Kusto 쿼리 언어를 사용 하 여 임시 탐색에 최적화 된 [Azure 데이터 탐색기 웹 UI](web-query-data.md) 를 사용 합니다.

* **복합 모델** - [복합 모델](https://docs.microsoft.com/power-bi/desktop-composite-models) 을 사용 하 여 최상위 대시보드에 대 한 집계 데이터를 필터링 된 작업 원시 데이터와 결합할 수 있습니다. 원시 데이터를 사용 하는 시기와 집계 뷰를 사용 하는 시기를 명확 하 게 정의할 수 있습니다. 

* **가져오기 모드와 DirectQuery 모드 비교** -더 작은 데이터 집합의 상호 작용을 위해 **가져오기** 모드를 사용 합니다. 자주 업데이트 되는 크고 많은 데이터 집합에는 **DirectQuery** 모드를 사용 합니다. 예를 들어 **가져오기** 모드를 사용 하 여 차원 테이블이 작으며 자주 변경 되지 않으므로이를 사용 하 여 차원 테이블을 만듭니다. 데이터 업데이트의 예상 률에 따라 새로 고침 간격을 설정 합니다. **DirectQuery** 모드를 사용 하 여 팩트 테이블을 만듭니다. 이러한 테이블은 크고 원시 데이터를 포함 하기 때문입니다. Power BI [드릴스루](https://docs.microsoft.com/power-bi/desktop-drillthrough)를 사용 하 여 필터링 된 데이터를 표시 하려면 다음 표를 사용 합니다.

* **병렬 처리** – Azure 데이터 탐색기는 선형 확장 가능 데이터 플랫폼 이므로 다음과 같이 종단 간 흐름의 병렬 처리를 증가 시켜 대시보드 렌더링의 성능을 향상 시킬 수 있습니다.

  * [Power BI에서 DirectQuery의 동시 연결](https://docs.microsoft.com/power-bi/desktop-directquery-about#maximum-number-of-connections-option-for-directquery)수를 늘립니다.

  * [병렬 처리를 향상 시키기 위해 약한 일관성을](kusto/concepts/queryconsistency.md)사용 합니다. 이는 데이터의 새로 고침에 영향을 미칠 수 있습니다.

* **효과적인 슬라이서** – [동기화 슬라이서](https://docs.microsoft.com/power-bi/visuals/power-bi-visualization-slicers#sync-and-use-slicers-on-other-pages) 를 사용 하 여 보고서가 준비 되기 전에 데이터를 로드 하지 못하도록 합니다. 데이터 집합을 구조화 하 고 모든 시각적 개체를 배치한 후 모든 슬라이서를 표시 한 후에는 동기화 슬라이서를 선택 하 여 필요한 데이터만 로드할 수 있습니다.

* **필터 사용** -가능한 한 많은 Power BI 필터를 사용 하 여 Azure 데이터 탐색기 검색에 관련 데이터 분할 집중 합니다.

* **효율적인 시각적 개체** – 데이터에 가장 적합 한 시각적 개체를 선택 합니다.


## <a name="tips-for-using-the-azure-data-explorer-connector-for-power-bi-to-query-data"></a>Azure 데이터 탐색기 Power BI 커넥터를 사용 하 여 데이터를 쿼리 하기 위한 팁

다음 섹션에는 Power BI에서 Kusto 쿼리 언어를 사용 하기 위한 팁과 트릭이 포함 되어 있습니다. [Power BI Azure 데이터 탐색기 커넥터](power-bi-connector.md) 를 사용 하 여 데이터 시각화

### <a name="complex-queries-in-power-bi"></a>Power BI의 복잡 한 쿼리

복합 쿼리는 파워 쿼리 보다 Kusto에서 보다 쉽게 표현 됩니다. 이러한 [함수를 Kusto 함수로](kusto/query/functions/index.md)구현 하 고 Power BI에서 호출 해야 합니다. Kusto 쿼리의 문과 함께 **DirectQuery** 를 사용 하는 경우이 메서드가 필요 `let` 합니다. Power BI 두 개의 쿼리를 조인 하 고 `let` 문을 연산자와 함께 사용할 수 없기 때문에 `join` 구문 오류가 발생할 수 있습니다. 따라서 조인의 각 부분을 Kusto 함수로 저장 하 고 Power BI이 두 함수를 함께 조인할 수 있도록 허용 합니다.

### <a name="how-to-simulate-a-relative-date-time-operator"></a>상대 날짜-시간 연산자를 시뮬레이션 하는 방법

Power BI는와 같은 *상대* 날짜/시간 연산자를 포함 하지 않습니다 `ago()` .
시뮬레이션 하려면 `ago()` `DateTime.FixedLocalNow()` 및 `#duration` Power BI 함수 조합을 사용 합니다.

연산자를 사용 하 여이 쿼리 대신 `ago()` :

```kusto
    StormEvents | where StartTime > (now()-5d)
    StormEvents | where StartTime > ago(5d)
``` 

다음과 같은 쿼리를 사용 합니다.

```m
let
    Source = AzureDataExplorer.Contents("help", "Samples", "StormEvents", []),
    #"Filtered Rows" = Table.SelectRows(Source, each [StartTime] > (DateTime.FixedLocalNow()-#duration(5,0,0,0)))
in
    #"Filtered Rows"
```

### <a name="configuring-azure-data-explorer-connector-options-in-m-query"></a>M 쿼리에서 Azure 데이터 탐색기 커넥터 옵션 구성

M 쿼리 언어의 PBI 고급 편집기에서 Azure 데이터 탐색기 커넥터의 옵션을 구성할 수 있습니다. 이러한 옵션을 사용 하 여 Azure 데이터 탐색기 클러스터로 전송 되는 생성 된 쿼리를 제어할 수 있습니다.

```m
let
    Source = AzureDataExplorer.Contents("help", "Samples", "StormEvents", [<options>])
in
    Source
```

M 쿼리에서 다음 옵션 중 하나를 사용할 수 있습니다.

| 옵션 | 샘플 | Description
|---|---|---
| MaxRows | `[MaxRows=300000]` | `truncationmaxrecords`쿼리에 set 문을 추가 합니다. 쿼리가 호출자에 게 반환할 수 있는 기본 최대 레코드 수를 재정의 합니다 (잘림).
| MaxSize | `[MaxSize=4194304]` | `truncationmaxsize`쿼리에 set 문을 추가 합니다. 쿼리가 호출자에 게 반환할 수 있는 기본 최대 데이터 크기 (잘림)를 재정의 합니다.
| NoTruncate | `[NoTruncate=true]` | `notruncation`쿼리에 set 문을 추가 합니다. 호출자에 게 반환 되는 쿼리 결과를 무시 하도록 설정 합니다.
| AdditionalSetStatements | `[AdditionalSetStatements="set query_datascope=hotcache"]` | 제공 된 set 문을 쿼리에 추가 합니다. 이러한 문은 쿼리 기간에 대 한 쿼리 옵션을 설정 하는 데 사용 됩니다. 쿼리 옵션은 쿼리가 실행되고 결과가 반환되는 방법을 제어합니다.
| 구분 | `[CaseInsensitive=true]` | 커넥터에서 대/소문자를 구분 하지 않는 쿼리를 생성 하도록 합니다 `=~` . 쿼리는 값을 비교할 때 연산자 대신 연산자를 사용 `==` 합니다.
| 제한 시간 | `[Timeout=#duration(0,10,0,0)]` | 쿼리의 클라이언트 및 서버 시간 제한을 모두 제공 된 기간으로 구성 합니다.

> [!NOTE]
> 여러 옵션을 함께 결합 하 여 원하는 동작에 도달할 수 있습니다. `[NoTruncate=true, CaseInsensitive=true]`

### <a name="reaching-kusto-query-limits"></a>Kusto 쿼리 제한에 도달

Kusto 쿼리는 [쿼리 제한](kusto/concepts/querylimits.md)에 설명 된 대로 기본적으로 최대 50만 행 또는 64 MB를 반환 합니다. **Azure 데이터 탐색기 (Kusto)** 연결 창에서 **고급 옵션** 을 사용 하 여 이러한 기본값을 재정의할 수 있습니다.

![고급 옵션](media/power-bi-best-practices/advanced-options.png)

이러한 옵션은 쿼리를 사용 하 여 [set 문을](kusto/query/setstatement.md) 실행 하 여 기본 쿼리 제한을 변경 합니다.

* **쿼리 결과 레코드 수 제한** 에서을 생성 합니다. `set truncationmaxrecords`
* **쿼리 결과 데이터 크기 제한 (바이트)** 을 생성 합니다. `set truncationmaxsize`
* **비활성화 결과-잘림 집합** 을 생성 합니다. `set notruncation`

### <a name="case-sensitivity"></a>대/소문자 구분

기본적으로 커넥터는 문자열 값을 비교할 때 대/소문자 구분 연산자를 사용 하는 쿼리를 생성 합니다 `==` . 데이터의 대/소문자를 구분 하지 않는 경우에는 원하는 동작이 아닙니다. 생성 된 쿼리를 변경 하려면 커넥터 옵션을 사용 합니다 `CaseInsensitive` .

```m
let
    Source = AzureDataExplorer.Contents("help", "Samples", "StormEvents", [CaseInsensitive=true]),
    #"Filtered Rows" = Table.SelectRows(Source, each [State] == "aLaBama")
in
    #"Filtered Rows"
```

### <a name="using-query-parameters"></a>쿼리 매개 변수 사용

쿼리 [매개 변수](kusto/query/queryparametersstatement.md) 를 사용 하 여 쿼리를 동적으로 수정할 수 있습니다. 

#### <a name="using-a-query-parameter-in-the-connection-details"></a>연결 정보에 쿼리 매개 변수 사용

쿼리 매개 변수를 사용 하 여 쿼리의 정보를 필터링 하 고 쿼리 성능을 최적화할 수 있습니다.
 
**쿼리 편집** 창에서 **홈**  >  **고급 편집기**

1. 쿼리에서 다음 섹션을 찾습니다.

    ```m
    Source = AzureDataExplorer.Contents("<Cluster>", "<Database>", "<Query>", [])
    ```

   예를 들면 다음과 같습니다.

    ```m
    Source = AzureDataExplorer.Contents("Help", "Samples", "StormEvents | where State == 'ALABAMA' | take 100", [])
    ```

1. 쿼리의 관련 부분을 매개 변수로 바꿉니다. 쿼리를 여러 부분으로 분할 하 고 매개 변수와 함께 앰퍼샌드 (&)를 사용 하 여 다시 연결 합니다.

   예를 들어 위의 쿼리에서는 `State == 'ALABAMA'` 파트를 가져와서: 및로 분할 하 고 `State == '` `'` `State` 매개 변수 사이에 매개 변수를 추가 합니다.

    ```kusto
    "StormEvents | where State == '" & State & "' | take 100"
    ```

1. 쿼리에 따옴표가 포함 되어 있으면 해당 기호를 올바르게 인코딩합니다. 예를 들어 다음 쿼리는 다음과 같습니다.

   ```kusto
   "StormEvents | where State == "ALABAMA" | take 100"
   ```

   는 다음과 같이 두 개의 따옴표를 사용 하 여 **고급 편집기** 에 나타납니다.

   ```kusto
    "StormEvents | where State == ""ALABAMA"" | take 100"
   ```

   세 개의 따옴표를 사용 하 여 다음 쿼리로 바꾸어야 합니다.

   ```kusto
   "StormEvents | where State == """ & State & """ | take 100"
   ```

#### <a name="use-a-query-parameter-in-the-query-steps"></a>쿼리 단계에서 쿼리 매개 변수 사용

쿼리 매개 변수를 지 원하는 쿼리 단계에서 쿼리 매개 변수를 사용할 수 있습니다. 예를 들어 매개 변수의 값을 기준으로 결과를 필터링 합니다.

![매개 변수를 사용 하 여 결과 필터링](media/power-bi-best-practices/filter-using-parameter.png)

### <a name="dont-use-power-bi-data-refresh-scheduler-to-issue-control-commands-to-kusto"></a>Power BI 데이터 새로 고침 스케줄러를 사용 하 여 Kusto에 제어 명령 실행 안 함

Power BI는 데이터 원본에 대 한 쿼리를 정기적으로 실행할 수 있는 데이터 새로 고침 스케줄러를 포함 합니다. Power BI는 모든 쿼리를 읽기 전용으로 가정 하므로이 메커니즘을 사용 하 여 제어 명령을 Kusto로 예약할 수 없습니다.

### <a name="power-bi-can-send-only-short-lt2000-characters-queries-to-kusto"></a>Power BI 짧은 ( &lt; 2000 문자) 쿼리만 Kusto에 보낼 수 있습니다.

Power BI에서 쿼리를 실행 하면 _"DataSource. 오류: 웹에서 콘텐츠를 가져오지 못했습니다."_ 라는 오류가 발생 합니다. 쿼리는 2000 자를 초과할 수 있습니다. Power BI는 **powerquery** 를 사용 하 여 쿼리를 검색 중인 URI의 일부로 인코딩하는 HTTP GET 요청을 실행 하 여 Kusto를 쿼리 합니다. 따라서 Power BI에서 실행 된 Kusto 쿼리는 요청 URI의 최대 길이 (2000 자에서 작은 오프셋을 뺀 값)로 제한 됩니다. 이 문제를 해결 하기 위해 Kusto에 [저장 된 함수](kusto/query/schema-entities/stored-functions.md) 를 정의 하 고 쿼리에서 해당 함수를 사용 Power BI 수 있습니다.

## <a name="next-steps"></a>다음 단계

[Power BI용 Azure Data Explorer 커넥터를 사용하여 데이터 시각화](power-bi-connector.md)
