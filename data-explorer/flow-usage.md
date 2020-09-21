---
title: 전원 자동화를 위한 Azure 데이터 탐색기 커넥터의 사용 예 (미리 보기)
description: Azure 데이터 탐색기 connector를 사용 하 여 강력한 자동화를 위한 몇 가지 일반적인 사용 예를 알아보세요.
author: orspod
ms.author: orspodek
ms.reviewer: dorcohen
ms.service: data-explorer
ms.topic: how-to
ms.date: 03/15/2020
ms.openlocfilehash: 03422de8987e125b5565b0625434ef660426b40a
ms.sourcegitcommit: c2ab3176db4dd55ac9ca8eee52bbd24096d1277f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/17/2020
ms.locfileid: "90740272"
---
# <a name="usage-examples-for-azure-data-explorer-connector-to-power-automate-preview"></a>전원 자동화를 위한 Azure 데이터 탐색기 커넥터의 사용 예 (미리 보기)

Azure 데이터 탐색기 파워 자동화 (이전 Microsoft flow) 커넥터를 사용 하면 Azure 데이터 탐색기에서 [Microsoft 파워 자동화](https://flow.microsoft.com/)의 흐름 기능을 사용할 수 있습니다. 예약 되거나 트리거된 작업의 일부로 Kusto 쿼리 및 명령을 자동으로 실행할 수 있습니다. 이 문서에는 몇 가지 일반적인 전원 자동화 커넥터 사용 예가 포함 되어 있습니다.

자세한 내용은 [Azure 데이터 탐색기 파워 자동화 커넥터 (미리 보기)](flow.md)를 참조 하세요.

## <a name="power-automate-connector-and-your-sql-database"></a>전원 자동화 커넥터 및 SQL database

파워 자동화 커넥터를 사용 하 여 데이터를 쿼리하고 SQL database에서 집계 합니다.

> [!Note]
> 적은 양의 출력 데이터에 대해서만 전원 자동화 커넥터를 사용 합니다. SQL 삽입 작업은 각 행에 대해 개별적으로 수행 됩니다. 

![전원 자동화 커넥터를 사용 하 여 데이터를 쿼리 하는 스크린샷](./media/flow-usage/flow-sqlexample.png)

> [!IMPORTANT]
> **클러스터 이름** 필드에 클러스터 URL을 입력 합니다.

## <a name="push-data-to-a-microsoft-power-bi-dataset"></a>Microsoft Power BI 데이터 집합에 데이터 푸시

Power BI 커넥터를 사용 하 여 파워 자동화 커넥터를 사용 하 여 Kusto 쿼리를 통해 데이터를 Power BI 스트리밍 데이터 집합에 푸시할 수 있습니다.

1. 새 **쿼리 실행 및 결과 나열** 작업을 만듭니다.
1. **새 단계**를 선택합니다.
1. **작업 추가**를 선택 하 고 Power BI를 검색 합니다.
1. **Power BI**  >  **데이터 집합에 행 추가**Power BI 선택 합니다. 

    ![Power BI 커넥터의 스크린샷](./media/flow-usage/flow-powerbiconnector.png)

1. 데이터를 푸시할 **작업 영역**, 데이터 **집합**및 **테이블** 을 입력 합니다.
1. 동적 콘텐츠 대화 상자에서 데이터 집합 스키마 및 관련 Kusto 쿼리 결과를 포함 하는 **페이로드** 를 추가 합니다.

    ![Power BI 필드의 스크린샷](./media/flow-usage/flow-powerbifields.png)

흐름은 Kusto 쿼리 결과 테이블의 각 행에 대 한 Power BI 작업을 자동으로 적용 합니다. 

![각 행에 대 한 Power BI 동작의 스크린샷](./media/flow-usage/flow-powerbiforeach.png)

## <a name="conditional-queries"></a>조건부 쿼리

Kusto 쿼리 결과를 다음 전원 자동화 작업에 대 한 입력 또는 조건으로 사용할 수 있습니다.

다음 예제에서는 마지막 날에 발생 한 인시던트에 대해 Kusto를 쿼리 합니다. 해결 된 각 인시던트에 대해 여유 시간 메시지가 게시 되 고 푸시 알림이 생성 됩니다.
여전히 활성 상태인 각 인시던트에 대해 Kusto를 쿼리하여 유사한 인시던트에 대 한 자세한 정보를 제공 합니다. 해당 정보를 전자 메일로 보내고 Azure DevOps Server에서 관련 작업을 엽니다.

이와 비슷한 흐름을 만들려면 다음 지침을 따르세요.

1. 새 **쿼리 실행 및 결과 나열** 작업을 만듭니다.
1. **새 단계**  >  **조건 제어**를 선택 합니다.
1. 동적 콘텐츠 창에서 다음 작업의 조건으로 사용할 매개 변수를 선택 합니다.
1. 특정 매개 변수에 대 한 특정 조건을 설정 하려면 *관계* 유형 및 *값* 을 선택 합니다.

    :::image type="content" source="./media/flow-usage/flow-condition.png" alt-text="Kusto 쿼리 결과를 기반으로 흐름 조건을 사용 하 여 다음 흐름 작업을 결정 합니다. Azure 데이터 탐색기" lightbox="./media/flow-usage/flow-condition.png#lightbox":::

    흐름은이 조건을 쿼리 결과 테이블의 각 행에 적용 합니다.
1. 조건이 true 및 false 인 경우에 대 한 작업을 추가 합니다.

    :::image type="content" source="./media/flow-usage/flow-conditionactions.png" alt-text="조건이 true 또는 false 인 경우에 대 한 작업 추가, Kusto 쿼리 결과를 기반으로 하는 흐름 조건, Azure 데이터 탐색기" lightbox="./media/flow-usage/flow-conditionactions.png#lightbox":::

Kusto 쿼리 결과 값을 다음 작업에 대 한 입력으로 사용할 수 있습니다. 동적 콘텐츠 창에서 결과 값을 선택 합니다.
다음 예제에서는 Kusto 쿼리의 데이터를 포함 하는 **여유 시간 게시 메시지** 작업 및 **Visual Studio-새 작업 항목 만들기** 작업을 추가 합니다.

![여유 시간-메시지 게시 작업 스크린샷](./media/flow-usage/flow-slack.png)

![Visual Studio 작업 스크린샷](./media/flow-usage/flow-visualstudio.png)

이 예에서는 인시던트가 아직 활성 상태인 경우 Kusto를 다시 쿼리하여 동일한 소스의 인시던트가 과거에 해결 된 방법에 대 한 정보를 가져옵니다.

![흐름 조건 쿼리 스크린샷](./media/flow-usage/flow-conditionquery.png)

> [!IMPORTANT]
> **클러스터 이름** 필드에 클러스터 URL을 입력 합니다.

이 정보를 원형 차트로 시각화 하 고 팀에 전자 메일로 보냅니다.

![흐름 조건 전자 메일의 스크린샷](./media/flow-usage/flow-conditionemail.png)

## <a name="email-multiple-azure-data-explorer-flow-charts"></a>여러 Azure 데이터 탐색기 flow 차트를 메일로 보내기

1. 되풀이 트리거를 사용 하 여 새 흐름을 만들고 흐름의 간격 및 빈도를 정의 합니다. 
1. 하나 이상의 **Kusto 실행 쿼리 및 결과 시각화** 작업을 사용 하 여 새 단계를 추가 합니다. 

    ![흐름에서 여러 쿼리를 실행 하는 스크린샷](./media/flow-usage/flow-severalqueries.png)

1. 각 **Kusto 실행 쿼리와 결과** 작업을 시각화 하는 데 다음 필드를 정의 합니다.
    * 클러스터 URL.
    * 데이터베이스 이름입니다.
    * 쿼리 및 차트 종류 (예: HTML 테이블, 원형 차트, 시간 차트, 가로 막대형 차트 또는 사용자 지정 값).

    ![여러 첨부 파일을 사용 하 여 결과를 시각화 하는 스크린샷](./media/flow-usage/flow-visualizeresultsmultipleattachments.png)

1. **전자 메일 보내기 (v2)** 작업을 추가 합니다. 
    1. 본문 섹션에서 코드 보기 아이콘을 선택 합니다.
    1. **본문** 필드에서 쿼리의 시각화 된 결과가 전자 메일 본문에 포함 되도록 필요한 **BodyHtml** 을 삽입 합니다.
    1. 전자 메일에 첨부 파일을 추가 하려면 **첨부 파일 이름** 및 **첨부 파일 콘텐츠**를 추가 합니다.
    
    ![여러 첨부 파일을 전자 메일로 보내는 스크린샷](./media/flow-usage/flow-email-multiple-attachments.png)

    전자 메일 작업을 만드는 방법에 대 한 자세한 내용은 [쿼리 결과에 전자 메일 보내기](flow.md#email-kusto-query-results)를 참조 하세요. 

결과:

:::image type="content" source="./media/flow-usage/flow-resultsmultipleattachments.png" alt-text="원형 차트 및 가로 막대형 차트로 시각화 된 여러 전자 메일 첨부 파일의 결과, Azure 데이터 탐색기" lightbox="./media/flow-usage/flow-resultsmultipleattachments.png#lightbox":::

:::image type="content" source="./media/flow-usage/flow-resultsmultipleattachments2.png" alt-text="시간 차트로 시각화 된 여러 전자 메일 첨부 파일의 결과, Azure 데이터 탐색기" lightbox="./media/flow-usage/flow-resultsmultipleattachments2.png#lightbox":::

## <a name="next-steps"></a>다음 단계

예약 된 작업 또는 트리거된 작업의 일부로 Kusto 쿼리 및 명령을 자동으로 실행 하는 또 다른 방법인 [Azure Kusto 논리 앱 커넥터](kusto/tools/logicapps.md)에 대해 알아봅니다.

