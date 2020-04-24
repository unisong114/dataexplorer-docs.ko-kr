---
title: Azure 데이터 탐색기를 사용하여 Azure 모니터의 쿼리 데이터(미리 보기)
description: 이 항목에서는 응용 프로그램 인사이트 및 로그 분석을 사용하여 제품 간 쿼리에 대한 Azure Data Explorer 프록시를 만들어 Azure Monitor의 데이터를 쿼리합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/28/2020
ms.openlocfilehash: 983a9af42772209df2f48c1b1480e8ff0f34b5d6
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81501387"
---
# <a name="query-data-in-azure-monitor-using-azure-data-explorer-preview"></a>Azure 데이터 탐색기를 사용하여 Azure 모니터의 쿼리 데이터(미리 보기)

Azure 데이터 탐색기 프록시(ADX 프록시)는 [Azure 모니터](/azure/azure-monitor/) 서비스의 Azure 데이터 탐색기, [응용 프로그램 인사이트(AI)](/azure/azure-monitor/app/app-insights-overview)및 [로그 분석(LA)](/azure/azure-monitor/platform/data-platform-logs) 간에 교차 제품 쿼리를 수행할 수 있는 엔터티입니다. Azure 모니터 로그 분석 작업 영역 또는 응용 프로그램 인사이트 앱을 프록시 클러스터로 매핑할 수 있습니다. 그런 다음 Azure Data Explorer 도구를 사용하여 프록시 클러스터를 쿼리하고 교차 클러스터 쿼리에서 참조할 수 있습니다. 이 문서에서는 프록시 클러스터에 연결하고, Azure Data Explorer 웹 UI에 프록시 클러스터를 추가하고, Azure Data Explorer에서 AI 앱 또는 LA 작업 영역에 대한 쿼리를 실행하는 방법을 보여 줍니다.

Azure 데이터 탐색기 프록시 흐름: 

![ADX 프록시 흐름](media/adx-proxy/adx-proxy-flow.png)

## <a name="prerequisites"></a>사전 요구 사항

> [!NOTE]
> ADX 프록시가 미리 보기 모드에 있습니다. [프록시에 연결하여](#connect-to-the-proxy) 클러스터에 대한 ADX 프록시 기능을 활성화합니다. 질문이 있으시면 [ADXProxy](mailto:adxproxy@microsoft.com) 팀에 문의하십시오.

## <a name="connect-to-the-proxy"></a>프록시에 연결

1. Log Analytics 또는 응용 프로그램 인사이트 클러스터에 연결하기 전에 Azure Data Explorer 네이티브 클러스터(예: *도움말* 클러스터)가 왼쪽 메뉴에 나타나는지 확인합니다.

    ![ADX 네이티브 클러스터](media/adx-proxy/web-ui-help-cluster.png)

1. Azure 데이터 탐색기https://dataexplorer.azure.com/clusters)UI에서 ( : **클러스터 추가를**선택합니다.

1. 클러스터 **추가** 창에서 LA 또는 AI 클러스터에 URL을 추가합니다. 
    
    * LA의 경우:`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>`
    * AI의 경우:`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>`

    * **추가**를 선택합니다.

    ![클러스터 추가](media/adx-proxy/add-cluster.png)

    두 개 이상의 프록시 클러스터에 연결을 추가하는 경우 각 프록시 이름을 각각 지정합니다. 그렇지 않으면 모두 왼쪽 창에 같은 이름을 갖습니다.

1. 연결이 설정되면 LA 또는 AI 클러스터가 기본 ADX 클러스터와 함께 왼쪽 창에 나타납니다. 

    ![로그 분석 및 Azure 데이터 탐색기 클러스터](media/adx-proxy/la-adx-clusters.png)

## <a name="run-queries"></a>쿼리 실행

Kusto Explorer, ADX 웹 UI, Jupyter Kqlmagic, 흐름, PowerQuery, PowerShell, 자비스, 렌즈, REST API와 같은 Kusto 쿼리를 지원하는 클라이언트 도구를 사용하여 쿼리를 실행할 수 있습니다.

> [!TIP]
> * 데이터베이스 이름은 프록시 클러스터에 지정된 리소스와 이름이 같아야 합니다. 이름은 대/소문자를 구분합니다.
> * 클러스터 간 쿼리에서 응용 프로그램 인사이트 앱 및 Log Analytics 작업 영역의 명명이 올바른지 확인합니다.
>     * 이름에 특수 문자가 포함된 경우 프록시 클러스터 이름의 URL 인코딩으로 대체됩니다. 
>     * 이름에 [KQL 식별자 이름 규칙을](kusto/query/schema-entities/entity-names.md)충족하지 않는 문자가 포함된 **-** 경우 대시 문자로 바뀝니다.

### <a name="direct-query-from-your-la-or-ai-adx-proxy-cluster"></a>LA 또는 AI ADX 프록시 클러스터에서 직접 쿼리

LA 또는 AI 클러스터에서 쿼리를 실행합니다. 왼쪽 창에서 클러스터가 선택되었는지 확인합니다. 

```kusto
Perf | take 10 // Demonstrate query through the proxy on the LA workspace
```

![LA 작업 영역 쿼리](media/adx-proxy/query-la.png)

### <a name="cross-query-of-your-la-or-ai-adx-proxy-cluster-and-the-adx-native-cluster"></a>LA 또는 AI ADX 프록시 클러스터와 ADX 네이티브 클러스터의 교차 쿼리 

프록시에서 교차 클러스터 쿼리를 실행할 때 왼쪽 창에서 ADX 네이티브 클러스터가 선택되었는지 확인합니다. 다음 예제에서는 ADX 클러스터 테이블(사용)과 `union`LA 작업 영역을 결합하는 것을 보여 줍니다.

```kusto
union StormEvents, cluster('https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>').Perf
| take 10 
```

```kusto
let CL1 = 'https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>';
union <ADX table>, cluster(CL1).database(<workspace-name>).<table name>
```

   [![Azure 데이터 탐색기 프록시에서 교차 쿼리](media/adx-proxy/cross-query-adx-proxy.png)](media/adx-proxy/cross-query-adx-proxy.png#lightbox)

공용 구조화 대신 [ `join` 연산자(연산자)를](kusto/query/joinoperator.md)사용하면 프록시가 아닌 Azure Data Explorer 네이티브 클러스터에서 실행해야 [`hint`](kusto/query/joinoperator.md#join-hints) 할 수 있습니다. 

## <a name="additional-syntax-examples"></a>추가 구문 예제

다음 구문 옵션은 AI(응용 프로그램 인사이트) 또는 LA(로그 분석) 클러스터를 호출할 때 사용할 수 있습니다.

|구문 설명  |Application Insights  |Log Analytics  |
|----------------|---------|---------|
| 이 구독에 정의된 리소스만 포함하는 클러스터 내의**데이터베이스(클러스터 간 쿼리에 권장)** |   클러스터()`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>').database('<ai-app-name>`) | 클러스터()`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>`)     |
| 이 구독의 모든 앱/작업 영역이 포함된 클러스터    |     클러스터()`https://ade.applicationinsights.io/subscriptions/<subscription-id>`)    |    클러스터()`https://ade.loganalytics.io/subscriptions/<subscription-id>`)     |
|구독의 모든 앱/작업 영역을 포함하고 이 리소스 그룹의 구성원인 클러스터    |   클러스터()`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>`)      |    클러스터()`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>`)      |
|이 구독에 정의된 리소스만 포함하는 클러스터      |    클러스터()`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>`)    |  클러스터()`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>`)     |

## <a name="next-steps"></a>다음 단계

[쿼리 쓰기](write-queries.md)
