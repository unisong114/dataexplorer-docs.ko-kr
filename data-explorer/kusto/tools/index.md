---
title: Azure Data Explorer 도구 - Azure Data Explorer | Microsoft Docs
description: 이 문서에서는 Azure Data Explorer의 Azure Data Explorer 도구에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/01/2020
ms.openlocfilehash: ce7751d7ac60d23f9ffa0fc84992050fe1036131
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/13/2020
ms.locfileid: "83370478"
---
# <a name="azure-data-explorer-tools"></a>Azure Data Explorer 도구

## <a name="ad-hoc-query-tools"></a>임시 쿼리 도구


* [Kusto.Explorer](./kusto-explorer.md) - Kusto 쿼리 및 제어용 기본 데스크톱 도구
* [웹 UI](../../web-query-data.md) - Kusto 쿼리용 웹 UI

## <a name="visualizations-dashboards-and-reporting-tools"></a>시각화, 대시보드 및 보고 도구


* [Azure Notebooks](../../azure-notebooks.md) - Azure Notebooks를 사용하여 Azure Data Explorer의 데이터를 분석합니다.
* Excel
    * [Excel 빈 쿼리](../../excel-blank-query.md) - Kusto 쿼리를 Excel 데이터 원본으로 추가
    * [Excel 커넥터](../../excel-connector.md) - Azure Data Explorer용 Excel 커넥터 

* PowerBI

   * [PowerBI 모범 사례](../../power-bi-best-practices.md)
   * [PowerBI 커넥터](../../power-bi-connector.md)
   * [가져온 PowerBI 쿼리](../../power-bi-imported-query.md) 
   * [PowerBI SQL 쿼리](../../power-bi-sql-query.md)

* [Grafana](../../grafana.md)

## <a name="orchestration-tools"></a>오케스트레이션 도구


* Microsoft Flow
    * [Microsoft Flow 커넥터](../../flow.md)
    * [Microsoft Flow 커넥터 사용 예](../../flow-usage.md)
* [Microsoft Logic App](./logicapps.md) - Kusto 쿼리를 [Microsoft Logic App](https://docs.microsoft.com/azure/logic-apps/logic-apps-what-are-logic-apps)의 일부로 자동 실행



## <a name="data-ingestion-tools"></a>데이터 수집 도구


* [LightIngest](../../lightingest.md) - Azure Data Explorer로의 임시 데이터 수집에 대한 도움말 유틸리티
 



## <a name="source-control-integration-tools"></a>원본 제어 통합 도구

* [Azure Pipelines](../../devops.md) - 제어 명령을 파이프라인의 일부로 호출
* [Sync Kusto](./synckusto.md) - Git과의 Kusto 저장 함수 동기화
