---
title: Microsoft Excel로 가져온 Azure 데이터 탐색기 Kusto 쿼리를 사용하여 데이터 시각화
description: 이 문서에서는 Azure 데이터 탐색기 Kusto 쿼리를 Microsoft Excel로 가져오는 방법을 배웁니다.
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/30/2019
ms.openlocfilehash: 4999000e2084922b43b8085034f545d4b5c644a9
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81499528"
---
# <a name="visualize-data-using-an-azure-data-explorer-kusto-query-imported-into-microsoft-excel"></a>Microsoft Excel로 가져온 Azure 데이터 탐색기 Kusto 쿼리를 사용하여 데이터 시각화

Azure Data Explorer는 Excel의 데이터에 연결하기 위한 두 가지 옵션( 기본 커넥터 사용 또는 Azure Data Explorer에서 쿼리 가져오기)을 제공합니다. 이 문서에서는 Azure Data Explorer에서 Excel로 쿼리를 가져와 데이터를 시각화하는 방법을 보여 주며 이 문서에서는 Kusto 쿼리를 Excel 데이터 원본으로 추가하여 데이터에 대한 추가 계산 또는 시각화를 수행합니다.

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독이 아직 없는 경우 시작하기 전에 [Azure 체험 계정](https://azure.microsoft.com/free/)을 만듭니다.
* Azure Data [Explorer 도움말 클러스터에](https://dataexplorer.azure.com/clusters/help/databases/Samples) 연결할 수 있도록 Azure Active 디렉터리 구성원인 조직 전자 메일 계정 
<br>또는</br>
* [테스트 클러스터 및 데이터베이스를](create-cluster-database-portal.md) 만들고 [Azure 데이터 탐색기 웹 UI 응용 프로그램에](https://dataexplorer.azure.com/)로그인합니다.

## <a name="define-kusto-query-as-an-excel-data-source"></a>Kusto 쿼리를 Excel 데이터 원본으로 정의

1. [Azure 데이터 탐색기 웹 UI에서](https://dataexplorer.azure.com/clusters/help/databases/Samples)쿼리를 실행하고 결과를 확인합니다.

1. **공유** 탭을 선택하고 **Power BI에 대한 쿼리를**선택합니다.

    ![Power BI에 대한 웹 UI 쿼리](media/excel-blank-query/web-ui-query-to-powerbi.png)

1. 다음 알림과 함께 창이 나타납니다.

    ![클립보드로 쿼리 내보내기](media/excel-blank-query/query-exported-to-clipboard.png)

1. **오픈 마이크로 소프트 엑셀**.

1. **데이터** **탭에서 다른 소스** > **빈 쿼리에서** **데이터** > 가져옵니다.

    ![데이터 얻기 및 빈 쿼리 선택](media/excel-blank-query/get-data-blank-query.png)

1. **전원 쿼리 편집기** 창이 열립니다. 창에서 고급 **편집기**를 선택합니다.

    ![전원 쿼리 편집기 창](media/excel-blank-query/power-query-editor.png)

1. 고급 **편집기** 창에서 클립보드에 내보낸 쿼리를 붙여넣은 다음 **완료를**선택합니다.

    ![고급 편집기 쿼리](media/excel-blank-query/advanced-editor-query.png)    

1. 인증하려면 **자격 증명 편집을 선택합니다.**

    ![자격 증명 편집](media/excel-blank-query/edit-credentials.png)

1. **조직 계정을** 선택하고 **로그인합니다.** 로그인 프로세스를 완료한 다음 **연결을**선택합니다.

    ![완전한 로그인](media/excel-blank-query/complete-sign-in.png)

    이전 단계를 반복하여 쿼리를 추가합니다. 쿼리이름을 더 의미 있는 이름으로 바꿀 수 있습니다.

1. & **로드 닫기** 버튼을 선택하여 데이터를 Excel로 가져옵니다.

    ![닫기 및 로드 선택](media/excel-blank-query/close-and-load.png)

1. 이제 데이터가 Excel에 있습니다. 새로 **고침** 단추를 선택하여 쿼리를 새로 고칩니다.

    ![엑셀에서 데이터 보기](media/excel-blank-query/data-in-excel.png)

## <a name="next-steps"></a>다음 단계

[Excel용 Azure 데이터 탐색기 커넥터를 사용하여 데이터 시각화](excel-connector.md)