---
title: Azure Data Explorer를 사용하여 Azure Monitor에서 데이터 쿼리(미리 보기)
description: 이 항목에서는 Azure Data Explorer 제품 간 쿼리를 만들어 Azure Monitor(Application Insights 및 Log Analytics)에서 데이터를 쿼리합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: how-to
ms.date: 12/13/2020
ms.localizationpriority: high
ms.openlocfilehash: 607bcc7b1c0401116a94a9c06ff308a06ad936b7
ms.sourcegitcommit: d9e203a54b048030eeb6d05b01a65902ebe4e0b8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/14/2020
ms.locfileid: "97371580"
---
# <a name="query-data-in-azure-monitor-using-azure-data-explorer-preview"></a>Azure Data Explorer를 사용하여 Azure Monitor에서 데이터 쿼리(미리 보기)

Azure Data Explorer는 Azure Data Explorer, [AI(Application Insights)](/azure/azure-monitor/app/app-insights-overview) 및 [LA(Log Analytics)](/azure/azure-monitor/platform/data-platform-logs) 사이에서 서비스 간 쿼리를 지원합니다. Azure Data Explorer 쿼리 도구 및 서비스 간 쿼리를 사용하여 Log Analytics 또는 Application Insights 작업 영역을 쿼리할 수 있습니다. 이 문서에서는 서비스 간 쿼리를 만들고 Azure Data Explorer 웹 UI에 Log Analytics 또는 Application Insights 작업 영역을 추가하는 방법을 보여줍니다.

Azure Data Explorer 서비스 간 쿼리 흐름:

![Azure Data Explorer 프록시 흐름](media/query-monitor-data/query-monitor-workflow.png)

> [!NOTE]
> * Azure Data Explorer 클라이언트 도구를 사용하여 직접적으로 또는 Azure Data Explorer 클러스터에서 쿼리를 실행하여 간접적으로 Azure Data Explorer에서 Azure Monitor 데이터를 쿼리하는 기능은 미리 보기 모드입니다.
>* 도움이 필요하면 [서비스 간 쿼리 팀](mailto:adxproxy@microsoft.com)에 문의하세요.


## <a name="add-a-log-analyticsapplication-insights-workspace-to-azure-data-explorer-client-tools"></a>Azure Data Explorer 클라이언트 도구에 Log Analytics/Application Insights 작업 영역 추가

Azure Data Explorer 클라이언트 도구에 Log Analytics 또는 Application Insights 작업 영역을 추가하여 클러스터에 대한 서비스 간 쿼리를 사용합니다.

1. Log Analytics 또는 Application Insights 클러스터에 연결하기 전에 Azure Data Explorer 네이티브 클러스터(예: *help* 클러스터)가 왼쪽 메뉴에 표시되는지 확인합니다.

    ![Azure Data Explorer 네이티브 클러스터](media/query-monitor-data/web-ui-help-cluster.png)

1. Azure Data Explorer UI(https://dataexplorer.azure.com/clusters) )에서 **클러스터 추가** 를 선택합니다.

1. **클러스터 추가** 창에서 LA 또는 AI 클러스터의 URL을 추가합니다.

    * LA의 경우: `https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>`
    * AI의 경우: `https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>`

1. **추가** 를 선택합니다.

    ![클러스터 추가](media/query-monitor-data/add-cluster.png)

    >[!TIP]
    >둘 이상의 Log Analytics 또는 Application insights 작업 영역에 연결을 추가하는 경우 각각 다른 이름을 지정합니다. 그렇지 않은 경우 왼쪽 창에서 모두 동일한 이름을 갖게 됩니다.

1. 연결이 설정되면 Log Analytics 또는 Application Insights 작업 영역이 네이티브 Azure Data Explorer 클러스터와 함께 왼쪽 창에 나타납니다.

    ![Log Analytics 및 Azure Data Explorer 클러스터](media/query-monitor-data/la-adx-clusters.png)

> [!NOTE]
> 매핑할 수 있는 Azure Monitor 작업 영역 수는 100개로 제한됩니다.

## <a name="run-queries"></a>쿼리 실행

다음과 같이 Kusto 쿼리를 지원하는 클라이언트 도구를 사용하여 쿼리를 실행할 수 있습니다. Kusto Explorer, Azure Data Explorer Web UI, Jupyter Kqlmagic, Flow, PowerQuery, PowerShell, Lens, REST API.

> [!NOTE]
> 서비스 간 쿼리 기능은 데이터 검색에만 사용됩니다. 자세한 내용은 [함수 지원 가능성](#function-supportability)을 참조하세요.

> [!TIP]
> * 데이터베이스는 서비스 간 쿼리에서 지정된 리소스 이름과 같아야 합니다. 이름은 대/소문자를 구분합니다.
> * 서비스 간 쿼리에서 Application Insights 앱 및 Log Analytics 작업 영역의 이름이 올바른지 확인합니다.
> * 이름에 특수 문자가 포함된 경우 서비스 간 쿼리에서 URL 인코딩으로 바꿉니다.
> * 이름에 [KQL 식별자 이름 규칙](kusto/query/schema-entities/entity-names.md)을 충족하지 않는 문자가 포함된 경우 대시( **-** ) 문자로 바꿉니다.

### <a name="direct-query-on-your-log-analytics-or-application-insights-workspaces-from-azure-data-explorer-client-tools"></a>Azure Data Explorer 클라이언트 도구에서 Log Analytics 또는 Application Insights 작업 영역에 대한 직접 쿼리

Azure Data Explorer 클라이언트 도구에서 Log Analytics 또는 Application Insights 작업 영역에 대한 쿼리를 실행할 수 있습니다. 

1. 왼쪽 창에서 작업 영역이 선택되어 있는지 확인합니다.

1. 다음 쿼리를 실행합니다.

```kusto
Perf | take 10 // Demonstrate cross-service query on the Log Analytics workspace
```

![Log Analytics 작업 영역 쿼리](media/query-monitor-data/query-la.png)

### <a name="cross-query-of-your-log-analytics-or-application-insights-workspace-and-the-azure-data-explorer-native-cluster"></a>Log Analytics 또는 Application Insights 작업 영역 및 Azure Data Explorer 네이티브 클러스터의 교차 쿼리

클러스터 간 서비스 쿼리를 실행하는 경우 왼쪽 창에서 Azure Data Explorer 네이티브 클러스터가 선택되어 있는지 확인합니다. 다음 예제에서는 Azure Data Explorer 클러스터 테이블(`union` 사용)과 Log Analytics 작업 영역을 결합하는 방법을 보여줍니다.

다음 쿼리를 실행합니다.

```kusto
union StormEvents, cluster('https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>').Perf
| take 10
```

```kusto
let CL1 = 'https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>';
union <ADX table>, cluster(CL1).database(<workspace-name>).<table name>
```

   [ ![Azure Data Explorer에서 서비스 간 쿼리](media/query-monitor-data/cross-query.png)](media/query-monitor-data/cross-query.png#lightbox)

> [!TIP]
> union 대신 [`join` 연산자](kusto/query/joinoperator.md)를 사용하는 경우 Azure Data Explorer 네이티브 클러스터에서 이를 실행하려면 [`hint`](kusto/query/joinoperator.md#join-hints)가 필요할 수 있습니다.

### <a name="join-data-from-an-azure-data-explorer-cluster-in-one-tenant-with-an-azure-monitor-resource-in-another"></a>다른 테넌트에서 Azure Monitor 리소스를 사용하여 한 테넌트의 Azure Data Explorer 클러스터에서 데이터 조인

서비스 사이에서 테넌트 간 쿼리는 지원되지 않습니다. 두 리소스 모두로 확장하여 쿼리를 실행하기 위해 단일 테넌트에 로그인되어 있습니다.

Azure Data Explorer 리소스가 테넌트 'A'에 있고 Log Analytics 작업 영역이 테넌트 'B'에 있는 경우 다음 두 가지 방법 중 하나를 사용합니다.

1. Azure Data Explorer를 사용하면 다른 테넌트의 보안 주체에 대한 역할을 추가할 수 있습니다. 테넌트 'B'의 사용자 ID를 Azure Data Explorer 클러스터에 대한 권한 있는 사용자로 추가합니다. 테넌트 'B'가 Azure Data Explorer 클러스터의 *['TrustedExternalTenant'](/powershell/module/az.kusto/update-azkustocluster)* 속성에 포함되어 있는지 확인합니다. 테넌트 'B'에서 교차 쿼리를 완전히 실행합니다.

2. [Lighthouse](/azure/lighthouse/)를 사용하여 Azure Monitor 리소스를 테넌트 'A'에 프로젝션합니다.

### <a name="connect-to-azure-data-explorer-clusters-from-different-tenants"></a>다른 테넌트에서 Azure Data Explorer 클러스터에 연결

Kusto 탐색기는 사용자 계정이 원래 속한 테넌트에 자동으로 로그인합니다. 동일한 사용자 계정을 사용하여 다른 테넌트의 리소스에 액세스하려면 다음과 같이 연결 문자열에서 `tenantId`를 명시적으로 지정해야 합니다. `Data Source=https://ade.applicationinsights.io/subscriptions/SubscriptionId/resourcegroups/ResourceGroupName;Initial Catalog=NetDefaultDB;AAD Federated Security=True;Authority ID=`**TenantId**

## <a name="function-supportability"></a>함수 지원 가능성

Azure Data Explorer 서비스 간 쿼리는 Application Insights 및 Log Analytics에 대한 함수를 모두 지원합니다.
이 기능을 사용하면 클러스터 간 쿼리를 통해 Azure Monitor 테이블 형식 함수를 직접 참조할 수 있습니다.
다음 명령은 서비스 간 쿼리에서 지원합니다.

* `.show functions`
* `.show function {FunctionName}`
* `.show database {DatabaseName} schema as json`

다음 이미지에서는 Azure Data Explorer 웹 UI에서 테이블 형식 함수를 쿼리하는 예를 보여 줍니다.
함수를 사용하려면 쿼리 창에서 이름을 실행합니다.

  [ ![Azure Data Explorer 웹 UI에서 테이블 형식 함수 쿼리](media/query-monitor-data/function-query.png)](media/query-monitor-data/function-query.png#lightbox)

## <a name="additional-syntax-examples"></a>추가 구문 예제

Application Insights 또는 Log Analytics 클러스터를 호출하는 경우 다음 구문 옵션을 사용할 수 있습니다.

|구문 설명  |Application Insights  |Log Analytics  |
|----------------|---------|---------|
| 이 구독에 정의된 리소스만 포함된 클러스터 내의 데이터베이스(**클러스터 간 쿼리에 추천됨**) |   cluster(`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>').database('<ai-app-name>`) | cluster(`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>`)     |
| 이 구독의 모든 앱/작업 영역이 포함된 클러스터    |     cluster(`https://ade.applicationinsights.io/subscriptions/<subscription-id>`)    |    cluster(`https://ade.loganalytics.io/subscriptions/<subscription-id>`)     |
|구독의 모든 앱/작업 영역이 포함되고 이 리소스 그룹의 멤버인 클러스터    |   cluster(`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>`)      |    cluster(`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>`)      |
|이 구독에 정의된 리소스만 포함된 클러스터      |    cluster(`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>`)    |  cluster(`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>`)     |

## <a name="next-steps"></a>다음 단계

[쿼리 작성](write-queries.md)