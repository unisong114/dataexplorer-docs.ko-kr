---
title: Azure Data Explorer에서 원클릭 수집을 사용하여 로컬 파일의 JSON 데이터를 기존 테이블에 수집
description: 원클릭 수집을 사용하여 간단하게 기존 Azure Data Explorer 테이블에 데이터를 수집(로드)합니다.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: how-to
ms.date: 03/29/2020
ms.openlocfilehash: 49195d39ed2c81a6f7dca685781cbe265e8843d3
ms.sourcegitcommit: 811cf98edefd919b412d80201400919eedcab5cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89274701"
---
# <a name="use-one-click-ingestion-to-ingest-json-data-from-a-local-file-to-an-existing-table-in-azure-data-explorer"></a>Azure Data Explorer에서 원클릭 수집을 사용하여 로컬 파일의 JSON 데이터를 기존 테이블에 수집


> [!div class="op_single_selector"]
> * [컨테이너의 CSV 데이터를 새 테이블에 수집](one-click-ingestion-new-table.md)
> * [로컬 파일의 JSON 데이터를 기존 테이블에 수집](one-click-ingestion-existing-table.md)

[원클릭 수집](ingest-data-one-click.md)을 사용하면 JSON, CSV 및 기타 형식의 데이터를 신속하게 테이블로 수집하고 간단하게 매핑 구조를 만들 수 있습니다. 스토리지, 로컬 파일 또는 컨테이너의 데이터를 일회성 또는 지속적인 수집 프로세스로 수집할 수 있습니다.  

이 문서에서는 직관적인 원클릭 마법사를 특정 사례에 사용하여 **로컬 파일**의 **JSON** 데이터를 **기존 테이블**에 수집하는 방법을 설명합니다. 동일한 프로세스를 살짝 변형하여 다양한 사용 사례를 처리합니다.

원클릭 수집의 개요와 필수 조건 목록은 [원클릭 수집](ingest-data-one-click.md)을 참조하세요.
다양한 데이터 원본 형식에 대한 내용은 [Azure Data Explorer에서 원클릭 수집을 사용하여 컨테이너의 CSV 데이터를 새 테이블에 수집](one-click-ingestion-new-table.md)을 참조하세요.

## <a name="ingest-new-data"></a>새 데이터 수집

웹 UI의 왼쪽 메뉴에서 *데이터베이스* 또는 *테이블*을 마우스 오른쪽 단추로 클릭하고 **새 데이터 수집(미리 보기)** 을 선택합니다.

   :::image type="content" source="media/one-click-ingestion-existing-table/one-click-ingestion-in-webui.png" alt-text="웹 UI에서 원클릭 수집 선택":::
 
## <a name="select-an-ingestion-type"></a>수집 유형 선택

1. **새 데이터 수집(미리 보기)** 창에서 **원본** 탭이 선택됩니다.

1. **테이블** 필드가 자동으로 채워지지 않으면 드롭다운 메뉴에서 기존 테이블 이름을 선택합니다.

    > [!NOTE]
    > *테이블* 행에서 **새 데이터 수집(미리 보기)** 을 선택하면 선택한 테이블 이름이 **프로젝트 세부 정보**에 표시됩니다.

1. **수집 형식**에서 다음 단계를 수행합니다.

   1. **파일에서**를 선택합니다.  
   1. **찾아보기**를 선택하여 파일을 찾거나, 파일을 필드로 끕니다.
    
      :::image type="content" source="media/one-click-ingestion-existing-table/from-file.png" alt-text="파일에서 원클릭 수집":::

 1. 데이터 샘플이 표시됩니다. 특정 문자로 시작하거나 끝나는 파일만 수집하도록 데이터를 필터링합니다. 
   
    >[!NOTE] 
    >필터를 조정하면 미리 보기가 자동으로 업데이트됩니다.
  
> [!TIP]
> **컨테이너에서** 수집하려면 [Azure Data Explorer에서 원클릭 수집을 사용하여 컨테이너의 CSV 데이터를 새 테이블에 수집](one-click-ingestion-new-table.md#select-an-ingestion-type)을 참조하세요.

## <a name="edit-the-schema"></a>스키마 편집

**스키마 편집**을 선택하여 테이블 열 구성을 살펴보고 편집합니다.

### <a name="map-columns"></a>열 매핑 

1. **맵 열** 대화 상자가 열립니다. 하나 이상의 원본 열 또는 특성을 Azure Data Explorer 열에 연결할 수 있습니다.
    * 새 매핑을 자동으로 설정하거나 기존 매핑을 사용합니다. 
    * **원본 열** 필드에 **대상 열**로 매핑할 열 이름을 입력합니다.
    * 매핑에서 열을 삭제하려면 휴지통 아이콘을 선택합니다.

      :::image type="content" source="media/one-click-ingestion-existing-table/map-columns.png" alt-text="열 매핑 창"::: 
    
1. **업데이트**를 선택합니다.
1. **스키마** 탭에서 다음을 수행합니다.
    * **압축 유형**은 원본 파일 이름에 의해 자동으로 선택됩니다. 이 경우 압축 형식은 **JSON**입니다.
        
    * **JSON**을 선택하는 경우 **JSON 수준**을 1~10 사이에서 선택해야 합니다. 이 수준에 따라 테이블 열 데이터 분할이 결정됩니다.

        :::image type="content" source="media/one-click-ingestion-existing-table/json-levels.png" alt-text="JSON 수준 선택":::
    
       > [!TIP]
       > **CSV** 파일을 사용하려면 [Azure Data Explorer에서 원클릭 수집을 사용하여 컨테이너의 CSV 데이터를 새 테이블에 수집](one-click-ingestion-new-table.md#edit-the-schema)을 참조하세요.

### <a name="edit-the-table"></a>테이블 편집 

기존 테이블에 데이터를 수집하면 테이블에 대해 수행할 수 있는 변경은 더욱 제한됩니다.

테이블에서 다음을 수행합니다. 
* 새 열 헤더를 선택하여 **새 열**, **열 삭제**, **오름차순 정렬** 또는 **내림차순 정렬**을 추가합니다. 
* 기존 열에서는 데이터 정렬만 사용할 수 있습니다.

[!INCLUDE [data-explorer-one-click-column-table](includes/data-explorer-one-click-column-table.md)]

[!INCLUDE [data-explorer-one-click-command-editor](includes/data-explorer-one-click-command-editor.md)]

## <a name="start-ingestion"></a>수집 시작

**수집 시작**을 선택하여 테이블 및 매핑을 만들고 데이터 수집을 시작합니다.

:::image type="content" source="media/one-click-ingestion-existing-table/start-ingestion.png" alt-text="수집 시작":::

## <a name="complete-data-ingestion"></a>데이터 수집 완료

데이터 수집이 성공적으로 완료되면 **데이터 수집 완료** 창에서 세 단계 모두 녹색 확인 표시가 나타납니다.

:::image type="content" source="media/one-click-ingestion-existing-table/one-click-data-ingestion-complete.png" alt-text="원클릭 수집 완료":::

> [!IMPORTANT]
> 컨테이너에서 지속적인 수집을 설정하려면 [Azure Data Explorer에서 원클릭 수집을 사용하여 컨테이너의 CSV 데이터를 새 테이블에 수집](one-click-ingestion-new-table.md#create-continuous-ingestion-for-container)을 참조하세요.

[!INCLUDE [data-explorer-one-click-ingestion-query-data](includes/data-explorer-one-click-ingestion-query-data.md)]

## <a name="next-steps"></a>다음 단계

* [Azure Data Explorer 웹 UI에서 데이터 쿼리](web-query-data.md)
* [Kusto 쿼리 언어를 사용하여 Azure Data Explorer에 대한 쿼리 작성](write-queries.md)
