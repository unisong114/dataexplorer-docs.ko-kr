---
title: Power BI를 사용하여 Azure 데이터 탐색기 데이터를 쿼리하고 시각화하는 모범 사례
description: 이 문서에서는 Power BI를 사용하여 Azure 데이터 탐색기 데이터를 쿼리하고 시각화하는 모범 사례를 알아봅니다.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/26/2019
ms.openlocfilehash: 5c5c27afc2c6fa0ee337d336b43222f5ea6583e8
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81502193"
---
# <a name="best-practices-for-using-power-bi-to-query-and-visualize-azure-data-explorer-data"></a>Power BI를 사용하여 Azure 데이터 탐색기 데이터를 쿼리하고 시각화하는 모범 사례

Azure 데이터 탐색기는 로그 및 원격 분석 데이터에 사용 가능한 빠르고 확장성이 우수한 데이터 탐색 서비스입니다. [Power BI는](https://docs.microsoft.com/power-bi/) 데이터를 시각화하고 조직 전체에서 결과를 공유할 수 있는 비즈니스 분석 솔루션입니다. Azure 데이터 탐색기는 Power BI의 데이터에 연결하기 위한 세 가지 옵션을 제공합니다. 기본 [제공 커넥터를](power-bi-connector.md)사용하거나 [Azure 데이터 탐색기에서 쿼리를 Power BI로 가져오거나](power-bi-imported-query.md) [SQL 쿼리를](power-bi-sql-query.md)사용합니다. 이 문서에서는 Power BI를 사용하여 Azure 데이터 탐색기 데이터를 쿼리하고 시각화하는 팁을 제공합니다. 

## <a name="best-practices-for-using-power-bi"></a>Power BI 를 사용하는 모범 사례 

테라바이트 의 새로운 원시 데이터로 작업할 때 다음 지침을 따라 Power BI 대시보드 및 보고서를 간편하고 업데이트합니다.

* **트래블 라이트** - 보고서에 필요한 데이터만 Power BI에 가져옵니다. 심층 대화형 분석을 위해 Kusto 쿼리 언어를 사용하여 임시 탐색에 최적화된 [Azure Data Explorer 웹 UI를](web-query-data.md) 사용합니다.

* **복합 모델** - [복합 모델을](https://docs.microsoft.com/power-bi/desktop-composite-models) 사용하여 최상위 대시보드에 집계된 데이터를 필터링된 운영 원시 데이터와 결합합니다. 원시 데이터를 사용할 시기와 집계된 뷰를 사용할 시기를 명확하게 정의할 수 있습니다. 

* **가져오기 모드 대 DirectQuery 모드** - 가져오기 **모드를** 사용하여 더 작은 데이터 세트의 상호 작용을 합니다. 자주 업데이트하는 대규모 데이터 집합에 **직접 쿼리** 모드를 사용합니다. 예를 들어 가져오기 모드가 작고 자주 변경되지 않으므로 **차원** 테이블을 만듭니다. 예상 데이터 업데이트 속도에 따라 새로 고침 간격을 설정합니다. 이러한 테이블은 크고 원시 데이터를 포함하므로 **DirectQuery** 모드를 사용하여 팩트 테이블을 만듭니다. 이러한 테이블을 사용하여 Power BI [드릴스루를](https://docs.microsoft.com/power-bi/desktop-drillthrough)사용하여 필터링된 데이터를 표시합니다.

* **병렬 처리** – Azure Data Explorer는 선형으로 확장 가능한 데이터 플랫폼이므로 다음과 같이 종단 간 흐름의 병렬 처리기능을 증가시켜 대시보드 렌더링성능을 향상시킬 수 있습니다.

   * [Power BI에서 DirectQuery에서 동시 연결 수를 늘립니다.](https://docs.microsoft.com/power-bi/desktop-directquery-about#maximum-number-of-connections-option-for-directquery)

   * 약한 일관성을 사용하여 [병렬 처리를 개선합니다.](kusto/concepts/queryconsistency.md) 이는 데이터의 최신성에 영향을 미칠 수 있습니다.

* **효과적인 슬라이서** – [동기화 슬라이서를](https://docs.microsoft.com/power-bi/visuals/power-bi-visualization-slicers#sync-and-use-slicers-on-other-pages) 사용하여 준비가 되기 전에 보고서가 데이터를 로드하지 못하도록 합니다. 데이터 집합을 구조화하고 모든 시각적 개체를 배치하고 모든 슬라이서를 표시한 후 동기화 슬라이서를 선택하여 필요한 데이터만 로드할 수 있습니다.

* **필터 사용** - 가능한 한 많은 Power BI 필터를 사용하여 Azure 데이터 탐색기 검색을 관련 데이터 샤드에 집중합니다.

* **효율적인 비주얼** – 데이터에 가장 뛰어난 시각적 개체를 선택합니다.

## <a name="tips-for-using-the-azure-data-explorer-connector-for-power-bi-to-query-data"></a>Power BI에 Azure 데이터 탐색기 커넥터를 사용하여 데이터를 쿼리하는 팁

다음 섹션에는 Power BI를 사용하여 Kusto 쿼리 언어를 사용하기 위한 팁과 트릭이 포함되어 있습니다. Power [BI에 Azure 데이터 탐색기 커넥터를](power-bi-connector.md) 사용하여 데이터를 시각화합니다.

### <a name="complex-queries-in-power-bi"></a>Power BI의 복잡한 쿼리

복잡한 쿼리는 Power 쿼리보다 Kusto에서 더 쉽게 표현됩니다. [Kusto 함수로](kusto/query/functions/index.md)구현 하 고 Power BI에서 호출 해야 합니다. 이 메서드는 Kusto `let` 쿼리의 문과 함께 **DirectQuery를** 사용할 때 필요합니다. Power BI는 두 개의 `let` 쿼리를 조인하고 명령문을 `join` 연산자와 함께 사용할 수 없으므로 구문 오류가 발생할 수 있습니다. 따라서 조인의 각 부분을 Kusto 함수로 저장하고 Power BI가 이러한 두 함수를 함께 조인할 수 있도록 합니다.

### <a name="how-to-simulate-a-relative-date-time-operator"></a>상대 날짜 시간 연산자 시뮬레이션 방법

Power BI에는 와 같은 `ago()` *상대적인* 날짜 시간 연산자가 포함되어 있지 않습니다.
시뮬레이션하려면 `ago()`Power BI `DateTime.FixedLocalNow()` 함수와 `#duration` 의 조합을 사용합니다.

연산자 사용 `ago()` 이 쿼리 대신 다음을 수행합니다.

```kusto
    StormEvents | where StartTime > (now()-5d)
    StormEvents | where StartTime > ago(5d)
``` 

다음과 같은 동일한 쿼리를 사용합니다.

```powerquery-m
let
    Source = Kusto.Contents("help", "Samples", "StormEvents", []),
    #"Filtered Rows" = Table.SelectRows(Source, each [StartTime] > (DateTime.FixedLocalNow()-#duration(5,0,0,0)))
in
    #"Filtered Rows"
```

### <a name="reaching-kusto-query-limits"></a>Kusto 쿼리 제한에 도달 

Kusto 쿼리는 쿼리 [제한에](kusto/concepts/querylimits.md)설명된 대로 기본적으로 최대 500,000개의 행 또는 64MB까지 반환됩니다. **Kusto(Azure 데이터 탐색기)** 연결 창에서 **고급 옵션을** 사용하여 이러한 기본값을 재정의할 수 있습니다.

![고급 옵션](media/power-bi-best-practices/advanced-options.png)

이러한 옵션은 기본 쿼리 제한을 변경하기 위해 쿼리와 함께 [set 문을](kusto/query/setstatement.md) 발행합니다.

  * **제한 쿼리 결과 레코드 번호는**`set truncationmaxrecords`
  * **Bytes에서 쿼리 결과 데이터 크기를 제한하면**`set truncationmaxsize`
  * **결과 집합 잘림 해제를 비활성화하면**`set notruncation`

### <a name="using-query-parameters"></a>쿼리 매개 변수 사용

[쿼리 매개 변수를](kusto/query/queryparametersstatement.md) 사용하여 쿼리를 동적으로 수정할 수 있습니다. 

#### <a name="using-a-query-parameter-in-the-connection-details"></a>연결 세부 정보에서 쿼리 매개 변수 사용

쿼리 매개 변수를 사용하여 쿼리의 정보를 필터링하고 쿼리 성능을 최적화합니다.
 
**쿼리 편집** 창에서 **홈** > **고급 편집기**

1. 쿼리의 다음 섹션을 찾습니다.

    ```powerquery-m
    Source = Kusto.Contents("<Cluster>", "<Database>", "<Query>", [])
    ```
   
   다음은 그 예입니다.

    ```powerquery-m
    Source = Kusto.Contents("Help", "Samples", "StormEvents | where State == 'ALABAMA' | take 100", [])
    ```

1. 쿼리의 관련 부분을 매개 변수로 바꿉습니다. 쿼리를 여러 부분으로 분할하고 매개 변수와 함께 ampersand(&)를 사용하여 쿼리를 다시 연결합니다.

   예를 들어 위의 쿼리에서 `State == 'ALABAMA'` 부분을 다음으로 `State == '` 분할하고 `'` `State` 매개 변수를 배치합니다.
   
    ```kusto
    "StormEvents | where State == '" & State & "' | take 100"
    ```

1. 쿼리에 따옴표가 포함된 경우 올바르게 인코딩합니다. 예를 들어 다음 쿼리는 다음과 같은 것입니다. 

   ```kusto
   "StormEvents | where State == "ALABAMA" | take 100" 
   ```

   고급 **편집기에는** 다음과 같이 두 개의 따옴표가 표시됩니다.

   ```kusto
    "StormEvents | where State == ""ALABAMA"" | take 100"
   ```

   다음 쿼리를 세 개의 따옴표로 바꿔야 합니다.

   ```kusto
   "StormEvents | where State == """ & State & """ | take 100"
   ```

#### <a name="use-a-query-parameter-in-the-query-steps"></a>쿼리 단계에서 쿼리 매개 변수 사용

이를 지원하는 모든 쿼리 단계에서 쿼리 매개 변수를 사용할 수 있습니다. 예를 들어 매개 변수 값을 기준으로 결과를 필터링합니다.

![매개 변수를 사용하여 결과 필터링](media/power-bi-best-practices/filter-using-parameter.png)

### <a name="dont-use-power-bi-data-refresh-scheduler-to-issue-control-commands-to-kusto"></a>Power BI 데이터 새로 고침 스케줄러를 사용하여 Kusto에 제어 명령을 발행하지 마십시오.

Power BI에는 데이터 원본에 대해 주기적으로 쿼리를 발행할 수 있는 데이터 새로 고침 스케줄러가 포함되어 있습니다. Power BI는 모든 쿼리가 읽기 전용이라고 가정하므로 이 메커니즘을 사용하여 Kusto에 제어 명령을 예약하면 안 됩니다.

### <a name="power-bi-can-send-only-short-lt2000-characters-queries-to-kusto"></a>Power BI는 Kusto에 짧은(2000자)&lt;쿼리만 보낼 수 있습니다.

Power BI에서 쿼리를 실행하면 _"DataSource.Error: Web.Contents에서 내용을 얻지 못했습니다..."라는_ 오류가 발생하는 경우 쿼리가 2000자보다 길어질 수 있습니다. Power BI는 **PowerQuery를** 사용하여 검색되는 URI의 일부로 쿼리를 인코딩하는 HTTP GET 요청을 실행하여 Kusto를 쿼리합니다. 따라서 Power BI에서 발급한 Kusto 쿼리는 요청 URI의 최대 길이(2000자, 작은 오프셋을 뺀 값)로 제한됩니다. 해결 방법으로 Kusto에 [저장된 함수를](kusto/query/schema-entities/stored-functions.md) 정의하고 쿼리에서 Power BI가 해당 함수를 사용하도록 할 수 있습니다.

## <a name="next-steps"></a>다음 단계

[Power BI용 Azure Data Explorer 커넥터를 사용하여 데이터 시각화](power-bi-connector.md)




