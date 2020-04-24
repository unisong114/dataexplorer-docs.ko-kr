---
title: 마이크로소프트 엑셀용 Azure 데이터 탐색기 커넥터를 사용하여 데이터 시각화
description: 이 문서에서는 Azure 데이터 탐색기용 Excel 커넥터를 사용하는 방법을 배웁니다.
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/30/2019
ms.openlocfilehash: 42f52581d8f2f80deb5d6250ed54ab64fc1ba4d3
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81499684"
---
# <a name="visualize-data-using-the-azure-data-explorer-connector-for-excel"></a>Excel용 Azure 데이터 탐색기 커넥터를 사용하여 데이터 시각화

Azure Data Explorer는 Excel의 데이터에 연결하기 위한 두 가지 옵션( 기본 커넥터 사용 또는 Azure Data Explorer에서 쿼리 가져오기)을 제공합니다. 이 문서에서는 Excel에서 기본 커넥터를 사용하고 Azure Data Explorer 클러스터에 연결하여 데이터를 얻고 시각화하는 방법을 보여 주습니다.

Azure Data Explorer Excel 네이티브 커넥터는 쿼리 결과를 Excel로 내보내는 기능을 제공합니다. 추가 계산 또는 시각화를 위해 KQL 쿼리를 Excel 데이터 원본으로 추가할 수도 있습니다.

## <a name="define-kusto-query-as-an-excel-data-source-and-load-the-data-to-excel"></a>Kusto 쿼리를 Excel 데이터 원본으로 정의하고 데이터를 Excel에 로드합니다.

1. **오픈 마이크로 소프트 엑셀**.
1. **데이터** 탭에서 Azure > **데이터 탐색기에서**데이터**From Azure** >  **가져옵니다.**

    ![Azure Data Explorer에서 데이터 가져오기](media/excel-connector/get-data-from-adx.png)

1. **Kusto(Azure 데이터 탐색기)** 창에서 다음 필드를 완료하고 **확인을**선택합니다.

    ![Azure 데이터 탐색기(쿠스토) 창](media/excel-connector/adx-connection-window.png)
    
    |필드   |Description |
    |---------|---------|
    |**클러스터**   |   클러스터 이름(필수)      |    
    |**데이터베이스**     |    데이터베이스의 이름      |    
    |**테이블 이름 또는 Azure 데이터 탐색기 쿼리**    |     테이블 또는 Azure 데이터 탐색기 쿼리의 이름    | 
    
    **고급 옵션:**

     |필드   |Description |
    |---------|---------|
    |**쿼리 결과 레코드 번호 제한**     |     Excel에 로드된 레코드 수 제한  |    
    |**쿼리 결과 데이터 크기 제한(바이트)**    |    데이터 크기 제한      |   
    |**결과 집합 잘림 해제**    |         |      
    |**추가 집합 문(세미콜론으로 구분)**    |    데이터 `set` 원본에 적용할 문 추가     |   

1.  **내비게이터** 창에서 올바른 테이블로 이동합니다. 테이블 미리 보기 창에서 **데이터 변환을** 선택하여 데이터를 변경하거나 **로드를** 선택하여 Excel에 로드합니다.

![테이블 미리 보기 창](media/excel-connector/navigate-table-preview-window.png)

   > [!TIP]
   > **데이터베이스** 및/또는 **테이블 이름 또는 Azure Data 탐색기 쿼리가** 이미 지정되어 있으면 올바른 테이블 미리 보기 창이 자동으로 열립니다. 

## <a name="analyze-and-visualize-data-in-excel"></a>Excel에서 데이터 분석 및 시각화

데이터가 엑셀 시트에서 사용할 수 있게 되면 관계 및 시각적 개체를 만들어 데이터를 분석, 요약 및 시각화할 수 있습니다. 

1.  테이블 **디자인** 탭에서 **피벗 테이블로 요약을**선택합니다. **피벗 테이블 만들기** 창에서 관련 테이블을 선택하고 **확인을**선택합니다.

    ![피벗 테이블 만들기](media/excel-connector/create-pivot-table.png)

1. **피벗 테이블 필드** 창에서 관련 테이블 열을 선택하여 요약 테이블을 만듭니다. 아래 예제에서는 **EventId** 및 **상태가** 선택됩니다.
    
    ![피벗 테이블 필드 선택](media/excel-connector/pivot-table-pick-fields.png)

1. **피벗 테이블 분석** 탭에서 **피벗차트를** 선택하여 테이블을 기반으로 시각적 개체를 만듭니다. 

    ![피벗 차트](media/excel-connector/pivot-table-analyze-pivotchart.png)

1. 아래 예제에서는 **이벤트 ID,** **시작 시간**및 이벤트 **유형을** 사용하여 날씨 이벤트에 대한 추가 정보를 볼 수 있습니다.

    ![데이터 시각화](media/excel-connector/visualize-excel-data.png)

1. 전체 대시보드를 만들어 데이터를 모니터링합니다.

## <a name="next-steps"></a>다음 단계

[Microsoft Excel로 가져온 Azure 데이터 탐색기 Kusto 쿼리를 사용하여 데이터 시각화](excel-blank-query.md)