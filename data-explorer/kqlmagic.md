---
title: Jupyter 노트북을 사용하여 Azure 데이터 탐색기의 데이터 분석
description: 이 항목에서는 Jupyter 노트북 및 Kqlmagic 확장을 사용하여 Azure 데이터 탐색기에서 데이터를 분석하는 방법을 보여 주입니다.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 33fe6750c355c8dc79bbbd9223166786f844a502
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81496954"
---
# <a name="use-a-jupyter-notebook-and-kqlmagic-extension-to-analyze-data-in-azure-data-explorer"></a>Jupyter 노트북 및 Kqlmagic 확장을 사용하여 Azure 데이터 탐색기의 데이터를 분석합니다.

Jupyter Notebook은 라이브 코드, 수식, 시각화 및 내레이션 텍스트를 포함하는 문서를 만들고 공유할 수 있는 오픈 소스 웹 애플리케이션입니다. 사용에는 데이터 정리 및 변환, 숫자 시뮬레이션, 통계 모델링, 데이터 시각화 및 기계 학습이 포함됩니다.
[Jupyter Notebook](https://jupyter.org/)은 추가 명령을 지원하여 커널의 기능을 확장하는 매직 함수를 지원합니다. KQL 매직은 Jupyter Notebook에서 Python 커널의 기능을 확장하는 명령이므로 Kusto 언어 쿼리를 기본적으로 실행할 수 있습니다. Python 및 Kusto 쿼리 언어를 쉽게 결합하여 `render` 명령과 통합된 서식 있는 Plot.ly 라이브러리를 통해 데이터를 쿼리하고 시각화할 수 있습니다. 쿼리 실행을 위한 데이터 원본이 지원됩니다. 이러한 데이터 원본에는 로그 및 원격 분석 데이터에 대한 빠르고 확장성이 뛰어난 데이터 탐색 서비스인 Azure Data Explorer와 Azure Monitor 로그 및 응용 프로그램 인사이트가 포함됩니다. KQL 매직은 Azure Notebooks, Jupyter Lab 및 Visual Studio Code Jupyter 확장에서도 작동합니다.

## <a name="prerequisites"></a>사전 요구 사항

- AAD(Azure Active Directory)의 멤버인 조직 메일 계정
- 로컬 머신에 설치된 Jupyter Notebook 또는 Azure Notebooks를 사용하고 샘플 [Azure Notebooks](https://kustomagicsamples-manojraheja.notebooks.azure.com/j/notebooks/Getting%20Started%20with%20kqlmagic%20on%20Azure%20Data%20Explorer.ipynb) 복제

## <a name="install-kql-magic-library"></a>KQL 매직 라이브러리 설치

1. 다음과 같이 KQL 매직을 설치합니다.

    ```python
    !pip install Kqlmagic --no-cache-dir  --upgrade
    ```
    > [!NOTE]
    > Azure Notebooks를 사용하는 경우 이 단계가 필요하지 않습니다.

1. 다음과 같이 KQL 매직을 로드합니다.

    ```python
    %reload_ext Kqlmagic
    ```
    > [!NOTE]
    > 커널 > 변경 커널 > 파이썬 3.6을 클릭하여 커널 버전을 파이썬 3.6으로 변경하십시오.
    
## <a name="connect-to-the-azure-data-explorer-help-cluster"></a>Azure Data Explorer 도움말 클러스터에 연결

다음 명령을 사용하여 ‘도움말’ 클러스터에 호스트된 ‘샘플’ 데이터베이스에 연결합니다.**** 타사 AAD 사용자의 경우 테넌트 이름 `Microsoft.com`을 AAD 테넌트로 바꿉니다.

```python
%kql AzureDataExplorer://tenant="Microsoft.com";code;cluster='help';database='Samples'
```

## <a name="query-and-visualize"></a>쿼리 및 시각화

[렌더링 연산자](kusto/query/renderoperator.md)를 사용하여 데이터를 쿼리하고 ploy.ly 라이브러리를 사용하여 데이터를 시각화합니다. 이 쿼리 및 시각화는 네이티브 KQL을 사용하는 통합 환경을 제공합니다. Kqlmagic은 `timepivot`, `pivotchart` 및 `ladderchart`를 제외한 대부분의 차트를 지원합니다. `kind`, `ysplit` 및 `accumulate`를 제외한 모든 특성에서 렌더링이 지원됩니다. 

### <a name="query-and-render-piechart"></a>원형 차트 쿼리 및 렌더링

```python
%%kql
StormEvents
| summarize statecount=count() by State
| sort by statecount 
| limit 10
| render piechart title="My Pie Chart by State"
```

### <a name="query-and-render-timechart"></a>시간 차트 쿼리 및 렌더링

```python
%%kql
StormEvents
| summarize count() by bin(StartTime,7d)
| render timechart
```

> [!NOTE]
> 이러한 차트는 대화형으로 작동합니다. 시간 범위를 선택하여 특정 시간을 확대합니다.

### <a name="customize-the-chart-colors"></a>차트 색 사용자 지정

기본 색상 표가 마음에 들지 않으면 팔레트 옵션을 사용하여 차트를 사용자 지정합니다. 사용 가능한 팔레트는 여기에서 찾을 수 있습니다: [KQL 매직 쿼리 차트 결과에 대한 색상 팔레트 선택](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FColorYourCharts.ipynb)

1. 팔레트 목록의 경우 다음을 수행합니다.

    ```python
    %kql --palettes -popup_window
    ```

1. `cool` 색상표를 선택하고 쿼리를 다시 렌더링합니다.

    ```python
    %%kql -palette_name "cool"
    StormEvents
    | summarize statecount=count() by State
    | sort by statecount
    | limit 10
    | render piechart title="My Pie Chart by State"
    ```

## <a name="parameterize-a-query-with-python"></a>Python을 사용하여 쿼리를 매개 변수화

KQL 매직을 통해 Kusto 쿼리 언어와 Python을 간단하게 바꿔서 사용할 수 있습니다. 자세히 알아보기: [파이썬으로 KQL 매직 쿼리를 매개 변수화](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FParametrizeYourQuery.ipynb)

### <a name="use-a-python-variable-in-your-kql-query"></a>KQL 쿼리에 Python 변수 사용

쿼리에 Python 변수 값을 사용하여 데이터를 필터링할 수 있습니다.

```python
statefilter = ["TEXAS", "KANSAS"]
```

```python
%%kql
let _state = statefilter;
StormEvents 
| where State in (_state) 
| summarize statecount=count() by bin(StartTime,1d), State
| render timechart title = "Trend"
```

### <a name="convert-query-results-to-pandas-dataframe"></a>쿼리 결과를 Pandas DataFrame으로 변환

Pandas DataFrame에서 KQL 쿼리의 결과에 액세스할 수 있습니다. `_kql_raw_result_` 변수를 통해 마지막으로 실행된 쿼리 결과에 액세스하고 다음과 같이 결과를 Pandas DataFrame으로 쉽게 변환합니다.

```python
df = _kql_raw_result_.to_dataframe()
df.head(10)
```

### <a name="example"></a>예제

다양한 분석 시나리오에서 많은 쿼리를 포함하고 한 쿼리의 결과를 후속 쿼리에 피드하는 재사용 가능한 Notebook을 만들 수 있습니다. 아래 예제에서는 Python 변수 `statefilter`를 사용하여 데이터를 필터링합니다.

1. 쿼리를 실행하여 `DamageProperty`가 최댓값인 상위 10개 주를 확인합니다.

    ```python
    %%kql
    StormEvents
    | summarize max(DamageProperty) by State
    | order by max_DamageProperty desc
    | limit 10
    ```

1. 쿼리를 실행하여 상위 주를 추출하고 Python 변수에 설정합니다.

    ```python
    df = _kql_raw_result_.to_dataframe()
    statefilter =df.loc[0].State
    statefilter
    ```

1. `let` 문과 Python 변수를 사용하여 쿼리를 실행합니다.

    ```python
    %%kql
    let _state = statefilter;
    StormEvents 
    | where State in (_state)
    | summarize statecount=count() by bin(StartTime,1d), State
    | render timechart title = "Trend"
    ```

1. help 명령을 실행합니다.

    ```python
    %kql --help "help"
    ```

> [!TIP]
> 사용 가능한 모든 구성에 `%config Kqlmagic`대한 정보를 받으려면 을 사용합니다. 연결 문제 및 잘못된 쿼리와 같은 Kusto 오류를 해결하고 캡처하려면`%config Kqlmagic.short_errors=False`

## <a name="next-steps"></a>다음 단계

help 명령을 실행하여 지원되는 모든 기능을 포함하는 다음 샘플 Notebook을 탐색합니다.
- [Azure Data Explorer용 KQL 매직 시작](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FQuickStart.ipynb) 
- [Application Insights용 KQL 매직 시작](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FQuickStartAI.ipynb) 
- [Azure 모니터 로그에 대한 KQL 매직 시작](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FQuickStartLA.ipynb) 
- [Python을 사용하여 KQL 매직 쿼리 매개 변수화](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FParametrizeYourQuery.ipynb) 
- [KQL 매직 쿼리 차트 결과에 대한 색상표 선택](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FColorYourCharts.ipynb)
