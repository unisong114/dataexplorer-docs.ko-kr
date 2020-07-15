---
title: Azure 데이터 탐색기 (미리 보기)를 사용 하 여 Azure Monitor에서 데이터 쿼리
description: 이 항목에서는 Application Insights 및 Log Analytics를 사용 하 여 교차곱 쿼리를 위한 Azure 데이터 탐색기 프록시를 만들어 Azure Monitor의 데이터를 쿼리 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/28/2020
ms.openlocfilehash: e5e4e2642d41d045c7fc49efaca78c35e217b0e5
ms.sourcegitcommit: f086298a6dd32790910350c7cd3b914b51d51226
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/14/2020
ms.locfileid: "86374686"
---
# <a name="query-data-in-azure-monitor-using-azure-data-explorer-preview"></a>Azure 데이터 탐색기 (미리 보기)를 사용 하 여 Azure Monitor에서 데이터 쿼리

ADX Proxy (Azure 데이터 탐색기 proxy cluster)는 [Azure Monitor](/azure/azure-monitor/) 서비스에서 azure 데이터 탐색기, [Application Insights (AI](/azure/azure-monitor/app/app-insights-overview)) 및 [Log Analytics (LA)](/azure/azure-monitor/platform/data-platform-logs) 간에 제품 간 쿼리를 수행할 수 있도록 하는 엔터티입니다. Azure Monitor Log Analytics 작업 영역 또는 Application Insights 앱을 프록시 클러스터로 매핑할 수 있습니다. 그런 다음 Azure 데이터 탐색기 도구를 사용 하 여 프록시 클러스터를 쿼리하고 클러스터 간 쿼리에서이를 참조할 수 있습니다. 이 문서에서는 프록시 클러스터에 연결 하 고, Azure 데이터 탐색기 웹 UI에 프록시 클러스터를 추가 하 고, Azure 데이터 탐색기에서 AI 앱 또는 LA 작업 영역에 대해 쿼리를 실행 하는 방법을 보여 줍니다.

Azure 데이터 탐색기 프록시 흐름: 

![ADX 프록시 흐름](media/adx-proxy/adx-proxy-flow.png)

## <a name="prerequisites"></a>필수 구성 요소

> [!NOTE]
> ADX 프록시는 미리 보기 모드입니다. [프록시에 연결](#connect-to-the-proxy) 하 여 클러스터에 대해 adx 프록시 기능을 사용 하도록 설정 합니다. [Adxproxy](mailto:adxproxy@microsoft.com) 팀에 질문을 합니다.

## <a name="connect-to-the-proxy"></a>프록시에 연결

1. Log Analytics 또는 Application Insights 클러스터에 연결 하기 전에 Azure 데이터 탐색기 기본 클러스터 (예: *help* cluster)가 왼쪽 메뉴에 나타나는지 확인 합니다.

    ![ADX native cluster](media/adx-proxy/web-ui-help-cluster.png)

1. Azure 데이터 탐색기 UI (에서 https://dataexplorer.azure.com/clusters) **클러스터 추가**를 선택 합니다.

1. **클러스터 추가** 창에서 LA 또는 AI 클러스터에 URL을 추가 합니다. 
    
    * LA의 경우:`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>`
    * AI의 경우:`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>`

    * **추가**를 선택합니다.

    ![클러스터 추가](media/adx-proxy/add-cluster.png)

    둘 이상의 프록시 클러스터에 연결을 추가 하는 경우 각각 다른 이름을 지정 합니다. 그렇지 않으면 모두 왼쪽 창에 같은 이름을 갖게 됩니다.

1. 연결이 설정 되 면 LA 또는 AI 클러스터가 기본 ADX 클러스터와 함께 왼쪽 창에 표시 됩니다. 

    ![Log Analytics 및 Azure 데이터 탐색기 클러스터](media/adx-proxy/la-adx-clusters.png)

> [!NOTE]
> 매핑될 수 있는 Azure Monitor 작업 영역 수는 100 개로 제한 됩니다.

## <a name="run-queries"></a>쿼리 실행

Kusto 탐색기, ADX Web UI, Jupyter Kqlmagic, Flow, PowerQuery, PowerShell, Jarvis, Lens, REST API 등의 Kusto 쿼리를 지 원하는 클라이언트 도구를 사용 하 여 쿼리를 실행할 수 있습니다.

> [!TIP]
> * 데이터베이스 이름에는 프록시 클러스터에 지정 된 리소스와 같은 이름을 지정 해야 합니다. 이름은 대/소문자를 구분 합니다.
> * 클러스터 간 쿼리에서 Application Insights 앱 및 Log Analytics 작업 영역의 이름이 올바른지 확인 합니다.
>     * 이름에 특수 문자가 포함 되어 있으면 프록시 클러스터 이름에서 URL 인코딩으로 바꿉니다. 
>     * 이름에 [KQL 식별자 이름 규칙](kusto/query/schema-entities/entity-names.md)을 충족 하지 않는 문자가 포함 된 경우에는 대시 문자로 대체 됩니다 **-** .

### <a name="direct-query-from-your-la-or-ai-adx-proxy-cluster"></a>LA 또는 AI ADX 프록시 클러스터에서 직접 쿼리

LA 또는 AI 클러스터에서 쿼리를 실행 합니다. 왼쪽 창에서 클러스터가 선택 되어 있는지 확인 합니다. 

```kusto
Perf | take 10 // Demonstrate query through the proxy on the LA workspace
```

![LA 작업 영역 쿼리](media/adx-proxy/query-la.png)

### <a name="cross-query-of-your-la-or-ai-adx-proxy-cluster-and-the-adx-native-cluster"></a>LA 또는 AI ADX 프록시 클러스터와 ADX native cluster의 크로스 쿼리 

프록시에서 클러스터 간 쿼리를 실행 하는 경우 왼쪽 창에서 ADX native cluster가 선택 되어 있는지 확인 합니다. 다음 예에서는 LA 작업 영역에서 ADX 클러스터 테이블 (사용)을 결합 하는 방법을 보여 줍니다 `union` .

```kusto
union StormEvents, cluster('https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>').Perf
| take 10 
```

```kusto
let CL1 = 'https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>';
union <ADX table>, cluster(CL1).database(<workspace-name>).<table name>
```

   [![Azure 데이터 탐색기 프록시의 크로스 쿼리](media/adx-proxy/cross-query-adx-proxy.png)](media/adx-proxy/cross-query-adx-proxy.png#lightbox)

연산자를 사용 하는 대신 [ `join` 연산자](kusto/query/joinoperator.md)를 사용 하 여 [`hint`](kusto/query/joinoperator.md#join-hints) Azure 데이터 탐색기 기본 클러스터 (프록시가 아닌)에서 실행 해야 할 수 있습니다. 

## <a name="function-supportability"></a>함수 지원 가능성

Azure 데이터 탐색기 프록시 클러스터는 Application Insights 및 Log Analytics에 대 한 기능을 모두 지원 합니다.
이 기능을 통해 클러스터 간 쿼리는 Azure Monitor 테이블 형식 함수를 직접 참조할 수 있습니다.
프록시는 다음 명령을 지원 합니다.

```kusto
.show functions
.show function {FunctionName}
.show database {DataBaseName} schema as json
```

> [!NOTE]
> Azure Monitor은 테이블 형식 함수만 지원 합니다. 테이블 형식 함수는 매개 변수를 지원 하지 않습니다.

## <a name="additional-syntax-examples"></a>추가 구문 예제

다음 구문 옵션은 AI (Application Insights) 또는 Log Analytics (LA) 클러스터를 호출할 때 사용할 수 있습니다.

|구문 설명  |Application Insights  |Log Analytics  |
|----------------|---------|---------|
| 이 구독에 정의 된 리소스를 포함 하는 클러스터 내의 데이터베이스 (**클러스터 간 쿼리에 권장**) |   cluster ( `https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>').database('<ai-app-name>` ) | cluster ( `https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>` )     |
| 이 구독의 모든 앱/작업 영역을 포함 하는 클러스터입니다.    |     cluster ( `https://ade.applicationinsights.io/subscriptions/<subscription-id>` )    |    cluster ( `https://ade.loganalytics.io/subscriptions/<subscription-id>` )     |
|구독의 모든 앱/작업 영역을 포함 하 고이 리소스 그룹의 구성원 인 클러스터    |   cluster ( `https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>` )      |    cluster ( `https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>` )      |
|이 구독에 정의 된 리소스를 포함 하는 클러스터      |    cluster ( `https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>` )    |  cluster ( `https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>` )     |

## <a name="next-steps"></a>다음 단계

[쿼리 작성](write-queries.md)
