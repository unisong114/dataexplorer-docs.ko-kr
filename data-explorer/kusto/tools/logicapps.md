---
title: 마이크로 소프트 로직 앱과 쿠스토 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기에서 Microsoft 논리 앱 및 Kusto에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/30/2019
ms.openlocfilehash: f7d719ece5df6eb3f6d4060a2fb07e7092902601
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81523820"
---
# <a name="microsoft-logic-app-and-kusto"></a>마이크로 소프트 로직 앱과 쿠스토

Azure Kusto 논리 앱 커넥터를 사용하면 [사용자가 Microsoft Logic 앱을](https://docs.microsoft.com/azure/logic-apps/logic-apps-what-are-logic-apps)사용하여 예약되거나 트리거된 작업의 일부로 Kusto 쿼리 및 명령을 자동으로 실행할 수 있습니다.

논리 앱 및 흐름 커넥터는 동일한 커넥터 위에 구축되므로 [Flow 설명서 페이지에](flow.md)언급된 것과 동일한 [제한 사항,](flow.md#limitations) [작업,](flow.md#azure-kusto-flow-actions) [인증](flow.md#authentication) 및 [사용 예제가](flow.md#usage-examples) 모두 적용됩니다.


## <a name="how-to-create-a-logic-app-with-azure-kusto"></a>Azure Kusto를 사용하여 논리 앱을 만드는 방법

Azure 포털을 열고 새 논리 앱 리소스 만들기를 클릭합니다.
원하는 이름, 구독, 그룹 및 위치를 추가하고 만들기를 클릭합니다.

![논리 앱 만들기](./Images/KustoTools-LogicApp/logicapp-createlogicapp.png "로직앱-창조로직앱")

논리 앱을 만든 후 편집 버튼을 클릭합니다.

![논리 앱 디자이너 편집](./Images/KustoTools-LogicApp/logicapp-editdesigner.png "로직앱 편집디자이너")

빈 논리 앱 만들기

![논리 앱 빈 템플릿](./Images/KustoTools-LogicApp/logicapp-blanktemplate.png "로직앱 블랭크 템플릿")

되풀이 작업을 추가한 다음 'Azure Kusto'를 선택합니다.

![로직 앱 쿠스토 플로우 커넥터](./Images/KustoTools-LogicApp/logicapp-kustoconnector.png "로직앱 쿠스토커넥터")