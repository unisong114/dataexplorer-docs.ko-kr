---
title: Python을 사용 하 여 Azure 데이터 탐색기 클러스터 & DB 만들기
description: Python을 사용하여 Azure Data Explorer 클러스터 및 데이터베이스를 만드는 방법을 알아봅니다.
author: orspod
ms.author: orspodek
ms.reviewer: lugoldbe
ms.service: data-explorer
ms.topic: how-to
ms.date: 06/03/2019
ms.openlocfilehash: c2986fd436c5a6257efb9a537753d993a0a57169
ms.sourcegitcommit: f354accde64317b731f21e558c52427ba1dd4830
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88872015"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-python"></a>Python을 사용하여 Azure Data Explorer 클러스터 및 데이터베이스 만들기

> [!div class="op_single_selector"]
> * [포털](create-cluster-database-portal.md)
> * [CLI](create-cluster-database-cli.md)
> * [PowerShell](create-cluster-database-powershell.md)
> * [C#](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
> * [ARM 템플릿](create-cluster-database-resource-manager.md)

이 문서에서는 Python을 사용 하 여 Azure 데이터 탐색기 클러스터와 데이터베이스를 만듭니다. Azure Data Explorer는 애플리케이션, 웹 사이트, IoT 디바이스 등으로부터 대량의 데이터 스트리밍에 대한 실시간 분석을 제공하는 빠른 속도의 완전 관리형 데이터 분석 서비스입니다. Azure 데이터 탐색기을 사용 하려면 먼저 클러스터를 만들고 해당 클러스터에 하나 이상의 데이터베이스를 만듭니다. 그런 다음 데이터를 데이터베이스에 수집 하거나 로드 하 여 쿼리를 실행할 수 있도록 합니다.

## <a name="prerequisites"></a>필수 구성 요소

* 활성 구독이 있는 Azure 계정. [체험 계정 만들기](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

* [Python 3.4 이상](https://www.python.org/downloads/).

* [리소스에 액세스할 수 있는 AZURE AD 응용 프로그램 및 서비스 주체](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)입니다. , 및에 대 한 값을 가져옵니다 `Directory (tenant) ID` `Application ID` `Client Secret` .

## <a name="install-python-package"></a>Python 패키지 설치

Azure Data Explorer(Kusto)용 Python 패키지를 설치하려면 경로에 Python이 있는 명령 프롬프트를 엽니다. 다음 명령을 실행합니다.

```
pip install azure-common
pip install azure-mgmt-kusto
```
## <a name="authentication"></a>인증
이 문서의 예제를 실행 하려면 리소스에 액세스할 수 있는 Azure AD 응용 프로그램 및 서비스 주체가 필요 합니다. [AZURE ad 응용 프로그램 만들기](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal) 를 선택 하 여 무료 Azure Ad 응용 프로그램을 만들고 구독 범위에서 역할 할당을 추가 합니다. 또한, 및를 가져오는 방법을 보여 `Directory (tenant) ID` 줍니다 `Application ID` `Client Secret` .

## <a name="create-the-azure-data-explorer-cluster"></a>Azure Data Explorer 클러스터 만들기

1. 다음 명령을 사용하여 클러스터를 만듭니다.

    ```Python
    from azure.mgmt.kusto import KustoManagementClient
    from azure.mgmt.kusto.models import Cluster, AzureSku
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

    location = 'Central US'
    sku_name = 'Standard_D13_v2'
    capacity = 5
    tier = "Standard"
    resource_group_name = 'testrg'
    cluster_name = 'mykustocluster'
    cluster = Cluster(location=location, sku=AzureSku(name=sku_name, capacity=capacity, tier=tier))
    
    kustoManagementClient = KustoManagementClient(credentials, subscription_id)
    
    cluster_operations = kustoManagementClient.clusters
    
    poller = cluster_operations.create_or_update(resource_group_name, cluster_name, cluster)
    ```

   |**설정** | **제안 값** | **필드 설명**|
   |---|---|---|
   | cluster_name | *mykustocluster* | 원하는 클러스터 이름입니다.|
   | sku_name | *Standard_D13_v2* | 클러스터에 사용될 SKU입니다. |
   | 계층 | *Standard* | SKU 계층입니다. |
   | 용량 | *number* | 클러스터의 인스턴스 수입니다. |
   | resource_group_name | *testrg* | 클러스터가 만들어질 리소스 그룹 이름입니다. |

    > [!NOTE]
    > **클러스터 만들기** 는 장기 실행 작업입니다. **Create_or_update** 메서드는 lropoller의 인스턴스를 반환 합니다. 자세한 정보를 보려면 [lropoller 러 클래스](/python/api/msrest/msrest.polling.lropoller?view=azure-python) 를 참조 하세요.

1. 다음 명령을 실행하여 클러스터가 성공적으로 만들어졌는지 확인합니다.

    ```Python
    cluster_operations.get(resource_group_name = resource_group_name, cluster_name= clusterName, custom_headers=None, raw=False)
    ```

결과에 값이 `Succeeded`인 `provisioningState`가 있으면 클러스터가 성공적으로 만들어진 것입니다.

## <a name="create-the-database-in-the-azure-data-explorer-cluster"></a>Azure Data Explorer 클러스터에 데이터베이스 만들기

1. 다음 명령을 사용하여 데이터베이스를 만듭니다.

    ```Python
    from azure.mgmt.kusto.models import Database
    from datetime import timedelta
    
    softDeletePeriod = timedelta(days=3650)
    hotCachePeriod = timedelta(days=3650)
    databaseName="mykustodatabase"
    
    database_operations = kusto_management_client.databases 
    _database = ReadWriteDatabase(location=location,
                        soft_delete_period=softDeletePeriod,
                        hot_cache_period=hotCachePeriod)
    
    #Returns an instance of LROPoller, see https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
    poller =database_operations.create_or_update(resource_group_name = resource_group_name, cluster_name = clusterName, database_name = databaseName, parameters = _database)
    ```

    > [!NOTE]
    > Python 버전 0.4.0을 사용 하는 경우 ReadWriteDatabase 대신 데이터베이스를 사용 합니다.

   |**설정** | **제안 값** | **필드 설명**|
   |---|---|---|
   | cluster_name | *mykustocluster* | 데이터베이스가 만들어지는 클러스터의 이름입니다.|
   | database_name | *mykustodatabase* | 데이터베이스의 이름입니다.|
   | resource_group_name | *testrg* | 클러스터가 만들어질 리소스 그룹 이름입니다. |
   | soft_delete_period | *3650 days, 0:00:00* | 데이터를 쿼리할 수 있도록 유지되는 시간입니다. |
   | hot_cache_period | *3650 days, 0:00:00* | 데이터가 캐시에 유지되는 시간입니다. |

1. 다음 명령을 실행하여 직접 만든 데이터베이스를 살펴봅니다.

    ```Python
    database_operations.get(resource_group_name = resource_group_name, cluster_name = clusterName, database_name = databaseName)
    ```

이제 클러스터와 데이터베이스가 있습니다.

## <a name="clean-up-resources"></a>리소스 정리

* 다른 문서를 따르려면 만든 리소스를 유지 합니다.
* 리소스를 정리하려면 클러스터를 삭제합니다. 클러스터를 삭제하면 해당 클러스터에 있는 모든 데이터베이스도 함께 삭제됩니다. 다음 명령을 사용하여 클러스터를 삭제합니다.

    ```Python
    cluster_operations.delete(resource_group_name = resource_group_name, cluster_name = clusterName)
    ```

## <a name="next-steps"></a>다음 단계

* [Azure 데이터 탐색기 Python 라이브러리를 사용 하 여 데이터 수집](python-ingest-data.md)
