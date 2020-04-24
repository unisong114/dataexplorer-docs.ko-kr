---
title: Azure 데이터 탐색기 ODBC 커넥터를 사용하여 Tableau 데이터를 시각화합니다.
description: 이 문서에서는 Azure 데이터 탐색기 연결에 대한 ODBC(개방형 데이터베이스 연결) 연결을 사용하여 Tableau를 사용하여 데이터를 시각화하는 방법을 알아봅니다.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 5fc98a0bb173bc926dc289b16c1aa8d1a0f5d9de
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81501127"
---
# <a name="visualize-data-from-azure-data-explorer-in-tableau"></a>Tableau에서 Azure 데이터 탐색기의 데이터 시각화

 [Tableau는](https://www.tableau.com/) 비즈니스 인텔리전스를 위한 시각적 분석 플랫폼입니다. Tableau에서 Azure 데이터 탐색기에 연결하고 샘플 클러스터에서 데이터를 가져오려면 SQL Server 개방형 데이터베이스 연결(ODBC) 드라이버를 사용합니다. 

## <a name="prerequisites"></a>사전 요구 사항

이 문서를 완료하려면 다음이 필요합니다.

* SQL Server ODBC 드라이버를 사용하여 [ODBC를 사용하여 Azure 데이터 탐색기에 연결하여](connect-odbc.md) Tableau에서 Azure 데이터 탐색기에 연결합니다. 

* Tableau 데스크톱, 전체 또는 [평가판](https://www.tableau.com/products/desktop/download) 버전.

* StormEvents 샘플 데이터가 포함된 클러스터입니다. 자세한 내용은 [Azure 데이터 탐색기 클러스터 및 데이터베이스 만들기 및](create-cluster-database-portal.md) Azure 데이터 [탐색기로 샘플 데이터 인제스트](ingest-sample-data.md)를 참조하세요.

    [!INCLUDE [data-explorer-storm-events](includes/data-explorer-storm-events.md)]

## <a name="visualize-data-in-tableau"></a>Tableau에서 데이터 시각화 

ODBC 구성이 완료되면 샘플 데이터를 Tableau로 가져올 수 있습니다.

1. Tableau 데스크톱에서 왼쪽 메뉴에서 **다른 데이터베이스(ODBC)를 선택합니다.**

    ![ODBC로 연결](media/tableau/connect-odbc.png)

1. **DSN의**경우 ODBC에 대해 만든 데이터 원본을 선택한 다음 **로그인을**선택합니다.

    ![ODBC 로그인](media/tableau/odbc-sign-in.png)

1. **데이터베이스의**경우 샘플 클러스터에서 *테스트데이터베이스*와 같은 데이터베이스를 선택합니다. **스키마의**경우 *dbo를*선택하고 **테이블의**경우 *StormEvents* 샘플 테이블을 선택합니다.

    ![데이터베이스 및 테이블 선택](media/tableau/select-database-table.png)

1. 이제 Tableau에서 샘플 데이터에 대한 스키마를 표시합니다. 데이터를 Tableau로 가져오려면 **지금 업데이트를 선택합니다.**

    ![데이터 업데이트](media/tableau/update-data.png)

    데이터를 가져오면 Tableau는 다음 이미지와 유사한 데이터 행을 표시합니다.

    ![결과 집합](media/tableau/result-set.png)

1. 이제 Azure 데이터 탐색기에서 가져온 데이터를 기반으로 Tableau에서 시각화를 만들 수 있습니다. 자세한 내용은 [Tableau 학습](https://www.tableau.com/learn)을 참조하십시오.

## <a name="next-steps"></a>다음 단계

* [Azure 데이터 탐색기용 쿼리 작성](write-queries.md)