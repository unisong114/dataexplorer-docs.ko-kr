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
ms.openlocfilehash: e9b274ae129f7cd15ba30edb24f8432c738f55ab
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81490653"
---
# <a name="azure-data-explorer-tools"></a>Azure Data Explorer 도구

## <a name="ad-hoc-query-tools"></a>임시 쿼리 도구


* [Kusto.Explorer](./kusto-explorer.md) - Kusto 쿼리 및 제어용 기본 데스크톱 도구
* [웹 UI](https://docs.microsoft.com/azure/data-explorer/web-query-data) - Kusto 쿼리용 웹 UI

## <a name="visualizations-dashboards-and-reporting-tools"></a>시각화, 대시보드 및 보고 도구


* [Azure Notebooks](https://docs.microsoft.com/azure/data-explorer/azure-notebooks) - Azure Notebooks를 사용하여 Azure Data Explorer의 데이터를 분석합니다.
* Excel
    * [Excel 빈 쿼리](https://docs.microsoft.com/azure/data-explorer/excel-blank-query) - Kusto 쿼리를 Excel 데이터 원본으로 추가
    * [Excel 커넥터](https://docs.microsoft.com/azure/data-explorer/excel-connector) - Azure Data Explorer용 Excel 커넥터 

* PowerBI

   * [PowerBI 모범 사례](https://docs.microsoft.com/azure/data-explorer/power-bi-best-practices)
   * [PowerBI 커넥터](https://docs.microsoft.com/azure/data-explorer/power-bi-connector)
   * [가져온 PowerBI 쿼리](https://docs.microsoft.com/azure/data-explorer/power-bi-imported-query) 
   * [PowerBI SQL 쿼리](https://docs.microsoft.com/azure/data-explorer/power-bi-sql-query)

* [Grafana](https://docs.microsoft.com/azure/data-explorer/grafana)

## <a name="orchestration-tools"></a>오케스트레이션 도구


* Microsoft Flow
    * [Microsoft Flow 커넥터](https://docs.microsoft.com/azure/data-explorer/flow)
    * [Microsoft Flow 커넥터 사용 예](https://docs.microsoft.com/azure/data-explorer/flow-usage)
* [Microsoft Logic App](./logicapps.md) - Kusto 쿼리를 [Microsoft Logic App](https://docs.microsoft.com/azure/logic-apps/logic-apps-what-are-logic-apps)의 일부로 자동 실행



## <a name="data-ingestion-tools"></a>데이터 수집 도구


* [LightIngest](https://docs.microsoft.com/azure/data-explorer/lightingest) - Azure Data Explorer로의 임시 데이터 수집에 대한 도움말 유틸리티
 



## <a name="source-control-integration-tools"></a>원본 제어 통합 도구

* [Azure Pipelines](https://docs.microsoft.com/azure/data-explorer/devops) - 제어 명령을 파이프라인의 일부로 호출
* [Sync Kusto](./synckusto.md) - Git과의 Kusto 저장 함수 동기화
