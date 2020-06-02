---
title: Azure 데이터 탐색기 데이터 시각화
description: Azure 데이터 탐색기 데이터를 시각화할 수 있는 여러 가지 방법에 대해 알아봅니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/02/2020
ms.openlocfilehash: b1351ceb9fe4b81a818ca41728a588dddfb4c5a2
ms.sourcegitcommit: 41cd88acc1fd79f320a8fe8012583d4c8522db78
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/02/2020
ms.locfileid: "84294681"
---
# <a name="data-visualization-with-azure-data-explorer"></a>Azure 데이터 탐색기를 사용 하 여 데이터 시각화 

Azure 데이터 탐색기은 방대한 양의 데이터에 대 한 복잡 한 분석 솔루션을 빌드하는 데 사용 되는 로그 및 원격 분석 데이터에 대 한 빠르고 확장성이 뛰어난 데이터 탐색 서비스입니다. Azure 데이터 탐색기는 다양 한 시각화 도구와 통합 되므로 데이터를 시각화 하 고 조직 전체에서 결과를 공유할 수 있습니다. 이 데이터를 실행 가능한 정보로 변환 하 여 비즈니스에 영향을 줄 수 있습니다.

데이터 시각화 및 보고는 데이터 분석 프로세스에서 매우 중요 한 단계입니다. Azure 데이터 탐색기는 시나리오 및 예산에 가장 적합 한 서비스를 사용할 수 있도록 많은 BI 서비스를 지원 합니다.

## <a name="azure-data-explorer-dashboards"></a>Azure Data Explorer 대시보드

Azure 데이터 탐색기 대시보드는 독립 실행형 웹 응용 프로그램, [웹 UI](web-query-data.md)에서 쿼리를 실행 하 고 대시보드를 작성할 수 있도록 하는 웹 응용 프로그램입니다. Azure 데이터 탐색기 대시보드는 세 가지 주요 이점을 제공 합니다.

* 기본적으로 웹 UI에서 Azure 데이터 탐색기 대시보드로 쿼리를 내보냅니다. 
* 웹 UI에서 데이터를 탐색 합니다.
* 최적화 된 대시보드 렌더링 성능.

자세한 내용은 [Azure 데이터 탐색기 대시보드를 사용 하 여 데이터 시각화](azure-data-explorer-dashboards.md)를 참조 하세요.

## <a name="kusto-query-language-visualizations"></a>Kusto 쿼리 언어 시각화

Kusto 쿼리 언어는 [`render operator`](kusto/query/renderoperator.md) 테이블, 원형 차트 및 가로 막대형 차트와 같은 다양 한 시각화를 제공 하 여 쿼리 결과를 나타냅니다. 쿼리 시각화는 변칙 검색 및 예측, 기계 학습 등에 유용 합니다.

## <a name="power-bi"></a>Power BI

Azure 데이터 탐색기는 다양 한 방법을 사용 하 여 [Power BI](https://powerbi.microsoft.com) 에 연결 하는 기능을 제공 합니다. 

  * [기본 제공 기본 Power BI 커넥터](power-bi-connector.md)

  * [Azure 데이터 탐색기에서 Power BI로 쿼리 가져오기](power-bi-imported-query.md)
 
  * [SQL 쿼리(SQL query)](power-bi-sql-query.md)

## <a name="microsoft-excel"></a>Microsoft Excel

Azure 데이터 탐색기는 [기본 제공 기본 Excel 커넥터](excel-connector.md)를 사용 하 여 [Microsoft excel](https://products.office.com/excel) 에 연결 하거나 azure 데이터 탐색기에서 Excel로 [쿼리를 가져오는](excel-blank-query.md) 기능을 제공 합니다.

## <a name="grafana"></a>Grafana

[Grafana](https://grafana.com) 는 azure 데이터 탐색기에서 데이터를 시각화할 수 있도록 하는 azure 데이터 탐색기 플러그 인을 제공 합니다. [Grafana에 대 한 데이터 원본으로 Azure 데이터 탐색기를 설정 하 고 데이터를 시각화](grafana.md)합니다. 

## <a name="kibana"></a>Kibana

Azure 데이터 탐색기는 오픈 소스 커넥터용 K2Bridge를 사용 하 여 [Kibana](https://www.elastic.co/guide/en/kibana/6.8/discover.html) 에 연결 하는 기능을 제공 합니다. [Kibana에 대 한 데이터 원본으로 Azure 데이터 탐색기를 설정 하 고 데이터를 시각화](k2bridge.md)합니다.

## <a name="odbc-connector"></a>ODBC 커넥터

Azure 데이터 탐색기는 odbc [(Open Database Connectivity) 커넥터](connect-odbc.md) 를 제공 하므로 odbc를 지 원하는 모든 응용 프로그램이 Azure 데이터 탐색기에 연결할 수 있습니다.

## <a name="tableau"></a>Tableau

Azure 데이터 탐색기는 [ODBC 커넥터](connect-odbc.md) 를 사용 하 여 [tableau](https://www.tableau.com) 에 연결 하 고 [tableau에서 데이터를 시각화](tableau.md)하는 기능을 제공 합니다.

## <a name="qlik"></a>Qlik

Azure 데이터 탐색기는 [ODBC 커넥터](connect-odbc.md) 를 사용 하 여 [qlik](https://www.qlik.com) 에 연결 하 고 qlik Sense 대시보드를 만들고 데이터를 시각화 하는 기능을 제공 합니다. 다음 비디오를 통해 Qlik를 사용 하 여 Azure 데이터 탐색기 데이터를 시각화 하는 방법을 배울 수 있습니다. 

> [!VIDEO https://www.youtube.com/embed/nhWIiBwxjjU]  

## <a name="sisense"></a>Sisense

Azure 데이터 탐색기는 JDBC 커넥터를 사용 하 여 [Sisense](https://www.sisense.com) 에 연결 하는 기능을 제공 합니다. [Sisense의 데이터 원본으로 Azure 데이터 탐색기를 설정 하 고 데이터를 시각화](sisense.md)합니다.

## <a name="redash"></a>Redash

[Redash](https://redash.io/) 를 사용 하 여 대시보드를 작성 하 고 데이터를 시각화할 수 있습니다. [Redash에 대 한 데이터 원본으로 Azure 데이터 탐색기를 설정 하 고 데이터를 시각화](redash.md)합니다.
