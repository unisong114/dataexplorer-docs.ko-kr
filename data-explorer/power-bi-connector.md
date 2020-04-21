---
title: Power BI용 Azure 데이터 탐색기 커넥터로 데이터 시각화
description: 이 문서에서는 Power BI에서 데이터를 시각화하기 위한 세 가지 옵션 중 하나인 Azure 데이터 탐색기의 Power BI 커넥터를 사용하는 방법을 알아봅니다.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: d162adec8ea0c5244fef601bf409d12432f4ce00
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81501998"
---
# <a name="visualize-data-using-the-azure-data-explorer-connector-for-power-bi"></a>Power BI용 Azure Data Explorer 커넥터를 사용하여 데이터 시각화

Azure 데이터 탐색기는 로그 및 원격 분석 데이터에 사용 가능한 빠르고 확장성이 우수한 데이터 탐색 서비스입니다. Power BI는 데이터를 시각화하고 조직 전체에서 결과를 공유할 수 있는 비즈니스 분석 솔루션입니다. Azure Data Explorer는 Power BI에서 데이터에 연결하기 위한 세 가지 옵션, 즉 기본 제공 커넥터 사용, Azure Data Explorer에서 쿼리 가져오기 또는 SQL 쿼리 사용을 제공합니다. 이 문서에서는 기본 제공 커넥터를 사용하여 데이터를 얻고 Power BI 보고서에서 시각화하는 방법을 보여 주었습니다. Power BI 대시보드를 만들기 위해 Azure 데이터 탐색기 네이티브 커넥터를 사용하는 것은 간단합니다. Power BI 커넥터는 [가져오기 및 직접 쿼리 연결 모드를](https://docs.microsoft.com/power-bi/desktop-directquery-about)지원합니다. 시나리오, 규모 및 성능 요구 사항에 따라 **가져오기** 또는 **DirectQuery** 모드를 사용하여 대시보드를 빌드할 수 있습니다. 

## <a name="prerequisites"></a>사전 요구 사항

이 문서를 완료하려면 다음이 필요합니다.

* Azure 구독이 아직 없는 경우 시작하기 전에 [Azure 체험 계정](https://azure.microsoft.com/free/)을 만듭니다.
* [Azure Data Explorer 도움말 클러스터](https://dataexplorer.azure.com/clusters/help/databases/samples)에 연결하기 위한 Active Directory 디렉터리의 구성원인 조직 이메일 계정
* [Power BI Desktop](https://powerbi.microsoft.com/get-started/)(**무료 다운로드** 선택)

## <a name="get-data-from-azure-data-explorer"></a>Azure Data Explorer에서 데이터 가져오기

먼저 Azure Data Explorer 도움말 클러스터에 연결한 후, *StormEvents* 테이블에서 데이터의 하위 세트를 불러옵니다. [!INCLUDE [data-explorer-storm-events](includes/data-explorer-storm-events.md)]

1. Power BI Desktop의 **홈** 탭에서 **데이터 가져오기**, **자세히**를 차례로 선택합니다.

    ![데이터 가져오기](media/power-bi-connector/get-data-more.png)

1. Azure *데이터 탐색기*검색, **Azure 데이터 탐색기를** 선택한 다음 **연결합니다.**

    ![데이터 검색 및 가져오기](media/power-bi-connector/search-get-data.png)

1. **Kusto(Azure 데이터 탐색기)** 화면에서 다음 정보가 있는 양식을 작성합니다.

    ![클러스터, 데이터베이스, 테이블 옵션](media/power-bi-connector/cluster-database-table.png)

    **설정** | **값** | **필드 설명**
    |---|---|---|
    | 클러스터 | *https://help.kusto.windows.net* | 도움말 클러스터의 URL입니다. 다른 클러스터의 경우 URL이 *\<ClusterName\>https://\< 형식입니다. 지역\>.kusto.windows.net*. |
    | 데이터베이스 | 비워 둠 | 연결 중인 클러스터에서 호스트되는 데이터베이스입니다. 이후 단계에서 이 데이터베이스를 선택하게 됩니다. |
    | 테이블 이름 | 비워 둠 | 데이터베이스의 테이블 중 하나 또는 <code>StormEvents \| take 1000</code> 이후 단계에서 이 데이터베이스를 선택하게 됩니다. |
    | 고급 옵션 | 비워 둠 | 쿼리에 대한 옵션(예: 결과 세트 크기)입니다. |
    | 데이터 연결 모드 | *DirectQuery* | Power BI가 데이터를 가져올지 또는 데이터 원본에 직접 연결될지를 결정합니다. 이 커넥터에서는 두 옵션을 모두 사용할 수 있습니다. |
    | | | |
    
    > [!NOTE]
    > **가져오기** 모드에서는 데이터가 Power BI로 이동됩니다. **DirectQuery** 모드에서는 Azure 데이터 탐색기 클러스터에서 직접 데이터가 쿼리됩니다.
    >
    > 다음과 같은 경우 **가져오기** 모드를 사용합니다.
    > * 데이터 집합이 작습니다.
    > * 거의 실시간 데이터가 필요하지 않습니다. 
    > * 데이터가 이미 집계되어 있거나 [Kusto에서 집계를](kusto/query/summarizeoperator.md#list-of-aggregation-functions) 수행합니다.    
    >
    > 다음과 같은 경우 **직접 쿼리** 모드를 사용합니다.
    > * 데이터 집합이 매우 큽습니다. 
    > * 거의 실시간 데이터가 필요합니다.   

1. 도움말 클러스터에 아직 연결되지 않은 경우 로그인합니다. 조직 계정을 사용하여 로그인한 다음 **연결**을 선택합니다.

    ![로그인](media/power-bi-connector/sign-in.png)

1. **탐색기** 화면에서 **샘플** 데이터베이스를 확장한 후 **StormEvents**, **편집**을 차례로 선택합니다.

    ![테이블 선택](media/power-bi-connector/select-table.png)

    테이블이 파워 쿼리 편집기에서 열리고, 여기서 데이터를 가져오기 전에 행과 열을 편집할 수 있습니다.

1. Power 쿼리 편집기에서 **DamageCrops** 열 옆에 있는 화살표를 선택하고 **내림차순 정렬**을 선택합니다.

    ![DamageCrops를 내림차순으로 정렬](media/power-bi-connector/sort-descending.png)

1. **홈** 탭에서 **행 유지**를 선택한 후 **상위 행 유지**를 선택합니다. 값 *1000*을 입력하여 정렬된 테이블의 상위 1000개 행을 표시합니다.

    ![상위 행 유지](media/power-bi-connector/keep-top-rows.png)

1. **홈** 에서 **닫기 및 적용**을 선택합니다.

    ![닫기 및 적용](media/power-bi-connector/close-apply.png)

## <a name="visualize-data-in-a-report"></a>보고서의 데이터 시각화

[!INCLUDE [data-explorer-power-bi-visualize-basic](includes/data-explorer-power-bi-visualize-basic.md)]

## <a name="clean-up-resources"></a>리소스 정리

이 문서에 대해 만든 보고서가 더 이상 필요하지 않으면 Power BI 데스크톱(.pbix) 파일을 삭제합니다.

## <a name="next-steps"></a>다음 단계

[Power BI에 Azure 데이터 탐색기 커넥터를 사용하여 데이터를 쿼리하는 팁](power-bi-best-practices.md#tips-for-using-the-azure-data-explorer-connector-for-power-bi-to-query-data)
