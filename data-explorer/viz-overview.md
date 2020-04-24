---
title: Azure 데이터 탐색기 데이터 시각화
description: Azure 데이터 탐색기 데이터를 시각화할 수 있는 다양한 방법에 대해 알아보기
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/30/2020
ms.openlocfilehash: 6d3b692d72b673b55e4bdc0f737b74b9c4a669d9
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81500789"
---
# <a name="data-visualization-with-azure-data-explorer"></a>Azure 데이터 탐색기를 사용하여 데이터 시각화 

Azure Data Explorer는 방대한 양의 데이터에 대한 복잡한 분석 솔루션을 빌드하는 데 사용되는 로그 및 원격 분석 데이터에 대한 빠르고 확장성이 뛰어난 데이터 탐색 서비스입니다. Azure Data Explorer는 다양한 시각화 도구와 통합되므로 데이터를 시각화하고 조직 전체에서 결과를 공유할 수 있습니다. 이 데이터는 실행 가능한 통찰력으로 변환되어 비즈니스에 영향을 미칠 수 있습니다.

데이터 시각화 및 보고는 데이터 분석 프로세스에서 중요한 단계입니다. Azure Data Explorer는 시나리오 및 예산에 가장 적합한 서비스를 사용할 수 있도록 많은 BI 서비스를 지원합니다.

## <a name="kusto-query-language-visualizations"></a>Kusto 쿼리 언어 시각화

Kusto 쿼리 [`render operator`](kusto/query/renderoperator.md) 언어는 쿼리 결과를 묘사하는 테이블, 원형 차트 및 막대 차트와 같은 다양한 시각화를 제공합니다. 쿼리 시각화는 변칙 검색 및 예측, 기계 학습 등에 유용합니다.

## <a name="power-bi"></a>Power BI

Azure 데이터 탐색기는 다음과 같은 다양한 방법을 사용하여 [Power BI에](https://powerbi.microsoft.com) 연결하는 기능을 제공합니다. 

  * [기본 기본 Power BI 커넥터 내장](/azure/data-explorer/power-bi-connector)

  * [Azure 데이터 탐색기에서 Power BI로 가져오기 쿼리](/azure/data-explorer/power-bi-imported-query)
 
  * [SQL 쿼리(SQL query)](/azure/data-explorer/power-bi-sql-query)

## <a name="microsoft-excel"></a>Microsoft Excel

Azure Data Explorer는 기본 기본 Excel [커넥터를](excel-connector.md)사용하여 [Microsoft Excel에](https://products.office.com/excel) 연결하거나 Azure 데이터 탐색기에서 Excel로 [쿼리를 가져오는](excel-blank-query.md) 기능을 제공합니다.

## <a name="grafana"></a>Grafana

[Grafana](https://grafana.com) Azure 데이터 탐색기에서 데이터를 시각화할 수 있는 Azure 데이터 탐색기 플러그인을 제공합니다. [Azure 데이터 탐색기를 Grafana의 데이터 원본으로 설정한 다음 데이터를 시각화합니다.](/azure/data-explorer/grafana) 

## <a name="kibana"></a>Kibana

Azure 데이터 탐색기는 오픈 소스 커넥터인 K2Bridge를 사용하여 [키바나(검색 페이지)에](https://www.elastic.co/guide/en/kibana/6.8/discover.html) 연결하는 기능을 제공합니다. [Azure 데이터 탐색기를 Kibana의 데이터 원본으로 설정한 다음 데이터를 시각화합니다.](/azure/data-explorer/k2bridge)

## <a name="odbc-connector"></a>ODBC 커넥터

Azure 데이터 탐색기는 ODBC를 지원하는 모든 응용 프로그램이 Azure 데이터 탐색기에 연결할 수 있도록 [개방형 데이터베이스 연결(ODBC) 커넥터를](connect-odbc.md) 제공합니다.

## <a name="tableau"></a>Tableau

Azure 데이터 탐색기는 [ODBC 커넥터를](/azure/data-explorer/connect-odbc) 사용하여 [Tableau에](https://www.tableau.com) 연결한 다음 [Tableau에서 데이터를 시각화하는](tableau.md)기능을 제공합니다.

## <a name="qlik"></a>Qlik

Azure 데이터 탐색기는 [ODBC 커넥터를](/azure/data-explorer/connect-odbc) 사용하여 [Qlik에](https://www.qlik.com) 연결한 다음 Qlik 감지 대시보드를 만들고 데이터를 시각화하는 기능을 제공합니다. 다음 비디오를 사용하여 Qlik를 사용하여 Azure 데이터 탐색기 데이터를 시각화하는 방법을 배울 수 있습니다. 

> [!VIDEO https://www.youtube.com/embed/nhWIiBwxjjU]  

## <a name="sisense"></a>Sisense

Azure 데이터 탐색기는 JDBC 커넥터를 사용하여 [Sisense에](https://www.sisense.com) 연결하는 기능을 제공합니다. [Azure 데이터 탐색기를 Sisense의 데이터 원본으로 설정한 다음 데이터를 시각화합니다.](/azure/data-explorer/sisense)

## <a name="redash"></a>Redash

[Redash를](https://redash.io/) 사용하여 대시보드를 빌드하고 데이터를 시각화할 수 있습니다. [Azure 데이터 탐색기를 Redash의 데이터 원본으로 설정한 다음 데이터를 시각화합니다.](/azure/data-explorer/redash)