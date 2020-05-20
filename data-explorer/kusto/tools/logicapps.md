---
title: Logic Apps를 사용 하 여 Kusto 쿼리를 자동으로 실행
description: Logic Apps를 사용 하 여 Kusto 쿼리 및 명령을 자동으로 실행 하 고 예약 하는 방법에 대해 알아봅니다.
author: orspod
ms.author: orspodek
ms.reviewer: docohe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: 8765635e0eea8c1d41640bc0393d39a0afa5f971
ms.sourcegitcommit: e66c5f4b833b4f6269bb7bfa5695519fcb11d9fa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83630173"
---
# <a name="microsoft-logic-app-and-azure-data-explorer"></a>Microsoft 논리 앱 및 Azure 데이터 탐색기

Azure Kusto 논리 앱 커넥터를 사용 하면 [Microsoft 논리 앱](https://docs.microsoft.com/azure/logic-apps/logic-apps-what-are-logic-apps) 커넥터를 사용 하 여 예약 되거나 트리거된 작업의 일부로 kusto 쿼리 및 명령을 자동으로 실행할 수 있습니다.

논리 앱과 흐름은 동일한 커넥터에서 빌드됩니다. 따라서 흐름 [설명서 페이지](flow.md)에 설명 된 대로 흐름에 적용 되는 [제한 사항](flow.md#limitations), [작업](flow.md#azure-kusto-flow-actions), [인증](flow.md#authentication) 및 [사용 예](flow.md#azure-kusto-flow-actions) 도 Logic Apps에 적용 됩니다.

## <a name="how-to-create-a-logic-app-with-azure-data-explorer"></a>Azure 데이터 탐색기을 사용 하 여 논리 앱을 만드는 방법

1. [Microsoft Azure portal](https://ms.portal.azure.com/)를 엽니다. 
1. 을 검색 `logic app` 하 고 선택 합니다.

    [![](./Images/logicapps/logicapp-search.png "Search for logic app")](./Images/logicapps/logicapp-search.png#lightbox)

1. **+ 추가**를 선택 합니다.

    ![논리 앱 추가](./Images/logicapps/logicapp-add.png)

1. 양식에 필요한 세부 정보를 입력 합니다.
    * Subscription
    * Resource group
    * 논리 앱 이름
    * 지역 또는 통합 서비스 환경
    * 위치
    * 로그 분석 설정 또는 해제
1. **검토 + 만들기**를 선택합니다.

    ![논리 앱 만들기](./Images/logicapps/logicapp-create-new.png)

1. 논리 앱을 만들 때 **편집**을 선택 합니다.

    ![논리 앱 디자이너 편집](./Images/logicapps/logicapp-editdesigner.png "logicapp-editdesigner")

1. 빈 논리 앱을 만듭니다.

    ![논리 앱 빈 템플릿](./Images/logicapps/logicapp-blanktemplate.png "logicapp-blanktemplate")

1. 되풀이 작업을 추가 하 고 **Azure Kusto를**선택 합니다.

    ![논리 앱 Kusto Flow 커넥터](./Images/logicapps/logicapp-kustoconnector.png "logicapp-kustoconnector")

## <a name="next-steps"></a>다음 단계

* 되풀이 동작을 구성 하는 방법에 대 한 자세한 내용은 [Flow 설명서 페이지](flow.md) 를 참조 하세요.
* 논리 앱 작업을 구성 하는 방법에 대 한 몇 가지 [사용 예](flow.md#azure-kusto-flow-actions) 를 살펴보세요.
