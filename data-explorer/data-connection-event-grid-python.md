---
title: 파이썬을 사용하여 Azure 데이터 탐색기의 이벤트 그리드 데이터 연결 만들기
description: 이 문서에서는 파이썬을 사용하여 Azure 데이터 탐색기용 이벤트 그리드 데이터 연결을 만드는 방법을 알아봅니다.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/07/2019
ms.openlocfilehash: 3d0634819eda78b9cf5fa1d135c82b906363ef90
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81497032"
---
# <a name="create-an-event-grid-data-connection-for-azure-data-explorer-by-using-python"></a>파이썬을 사용하여 Azure 데이터 탐색기의 이벤트 그리드 데이터 연결 만들기

> [!div class="op_single_selector"]
> * [포털](ingest-data-event-grid.md)
> * [C#](data-connection-event-grid-csharp.md)
> * [Python](data-connection-event-grid-python.md)
> * [Azure Resource Manager 템플릿](data-connection-event-grid-resource-manager.md)

이 문서에서는 파이썬을 사용하여 Azure 데이터 탐색기용 이벤트 그리드 데이터 연결을 만듭니다. Azure 데이터 탐색기는 로그 및 원격 분석 데이터에 사용 가능한 빠르고 확장성이 우수한 데이터 탐색 서비스입니다. Azure Data Explorer는 이벤트 허브, IoT 허브 및 Blob 컨테이너에 기록된 Blob에서 수집 또는 데이터 로드를 제공합니다.

## <a name="prerequisites"></a>사전 요구 사항

* 활성 구독이 있는 Azure 계정. [체험 계정 만들기](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

* [Python 3.4 이상](https://www.python.org/downloads/).

* [클러스터 및 데이터베이스](create-cluster-database-python.md).

* [테이블 및 열 매핑](net-standard-ingest-data.md#create-a-table-on-your-test-cluster).

* [데이터베이스 및 테이블 정책(선택](database-table-policies-csharp.md) 사항)입니다.

* [이벤트 그리드 구독이 있는 저장소 계정입니다.](ingest-data-event-grid.md#create-an-event-grid-subscription-in-your-storage-account)

[!INCLUDE [data-explorer-data-connection-install-package-python](includes/data-explorer-data-connection-install-package-python.md)]

[!INCLUDE [data-explorer-authentication](includes/data-explorer-authentication.md)]

## <a name="add-an-event-grid-data-connection"></a>이벤트 그리드 데이터 연결 추가

다음 예제에서는 이벤트 그리드 데이터 연결을 프로그래밍 방식으로 추가하는 방법을 보여 주며, Azure 포털을 사용하여 이벤트 그리드 데이터 연결을 추가하기 위해 [Azure 데이터 탐색기에서](ingest-data-event-grid.md#create-an-event-grid-data-connection-in-azure-data-explorer) 이벤트 그리드 데이터 연결 만들기를 참조하세요.


```Python
from azure.mgmt.kusto import KustoManagementClient
from azure.mgmt.kusto.models import EventGridDataConnection
from azure.common.credentials import ServicePrincipalCredentials

#Directory (tenant) ID
tenant_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Application ID
client_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Client Secret
client_secret = "xxxxxxxxxxxxxx"
subscription_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
credentials = ServicePrincipalCredentials(
        client_id=client_id,
        secret=client_secret,
        tenant=tenant_id
    )
kusto_management_client = KustoManagementClient(credentials, subscription_id)

resource_group_name = "testrg"
#The cluster and database that are created as part of the Prerequisites
cluster_name = "mykustocluster"
database_name = "mykustodatabase"
data_connection_name = "myeventhubconnect"
#The event hub and storage account that are created as part of the Prerequisites
event_hub_resource_id = "/subscriptions/xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx/resourceGroups/xxxxxx/providers/Microsoft.EventHub/namespaces/xxxxxx/eventhubs/xxxxxx"
storage_account_resource_id = "/subscriptions/xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx/resourceGroups/xxxxxx/providers/Microsoft.Storage/storageAccounts/xxxxxx"
consumer_group = "$Default"
location = "Central US"
#The table and column mapping that are created as part of the Prerequisites
table_name = "StormEvents"
mapping_rule_name = "StormEvents_CSV_Mapping"
data_format = "csv"

#Returns an instance of LROPoller, check https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
poller = kusto_management_client.data_connections.create_or_update(resource_group_name=resource_group_name, cluster_name=cluster_name, database_name=database_name, data_connection_name=data_connection_name,
                                            parameters=EventGridDataConnection(storage_account_resource_id=storage_account_resource_id, event_hub_resource_id=event_hub_resource_id, 
                                                                                consumer_group=consumer_group, table_name=table_name, location=location, mapping_rule_name=mapping_rule_name, data_format=data_format))
```
|**설정** | **제안 값** | **필드 설명**|
|---|---|---|
| tenant_id | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx* | 테넌트 ID 디렉터리 ID라고도 합니다.|
| subscription_id | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx* | 리소스 만들기에 사용하는 구독 ID입니다.|
| client_id | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx* | 테넌트의 리소스에 액세스할 수 있는 응용 프로그램의 클라이언트 ID입니다.|
| client_secret | *트리플 엑스 트리플 엑스 트리플 엑스 트리플 엑스* | 테넌트의 리소스에 액세스할 수 있는 응용 프로그램의 클라이언트 보안 입니다. |
| resource_group_name | *테서그 (것)에 대한* | 클러스터를 포함하는 리소스 그룹의 이름입니다.|
| cluster_name | *mykustocluster* | 클러스터의 이름입니다.|
| database_name | *mykustodatabase* | 클러스터의 대상 데이터베이스 이름입니다.|
| data_connection_name | *마이이벤트허브커넥트* | 데이터 연결의 원하는 이름입니다.|
| table_name | *스톰 이벤트* | 대상 데이터베이스의 대상 테이블 이름입니다.|
| mapping_rule_name | *StormEvents_CSV_Mapping* | 대상 테이블과 관련된 열 매핑의 이름입니다.|
| data_format | *Csv* | 메시지의 데이터 형식입니다.|
| event_hub_resource_id | *리소스 ID* | 이벤트 그리드가 이벤트를 보내도록 구성된 이벤트 허브의 리소스 ID입니다. |
| storage_account_resource_id | *리소스 ID* | 수집용 데이터를 보유하는 저장소 계정의 리소스 ID입니다. |
| consumer_group | *$Default* | 이벤트 허브의 소비자 그룹입니다.|
| 위치 | *미국 중부* | 데이터 연결 리소스의 위치입니다.|

[!INCLUDE [data-explorer-data-connection-clean-resources-python](includes/data-explorer-data-connection-clean-resources-python.md)]
