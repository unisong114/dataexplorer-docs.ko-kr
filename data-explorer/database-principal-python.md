---
title: 파이썬을 사용하여 Azure 데이터 탐색기용 데이터베이스 보안 주체 추가
description: 이 문서에서는 Python을 사용하여 Azure Data Explorer에 대한 데이터베이스 보안 주체를 추가하는 방법을 알아봅니다.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 02/03/2020
ms.openlocfilehash: 3ddb34692158fdc7e6d4f7786f84b8c059bc0621
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81498696"
---
# <a name="add-database-principals-for-azure-data-explorer-by-using-python"></a>파이썬을 사용하여 Azure 데이터 탐색기용 데이터베이스 보안 주체 추가

> [!div class="op_single_selector"]
> * [C#](database-principal-csharp.md)
> * [Python](database-principal-python.md)
> * [Azure Resource Manager 템플릿](database-principal-resource-manager.md)

Azure 데이터 탐색기는 로그 및 원격 분석 데이터에 사용 가능한 빠르고 확장성이 우수한 데이터 탐색 서비스입니다. 이 문서에서는 Python을 사용하여 Azure 데이터 탐색기의 데이터베이스 보안 주체를 추가합니다.

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독이 아직 없는 경우 시작하기 전에 [Azure 체험 계정](https://azure.microsoft.com/free/)을 만듭니다.
* [클러스터 및 데이터베이스 만들기](create-cluster-database-python.md)

## <a name="install-python-package"></a>Python 패키지 설치

Azure Data Explorer(Kusto)용 Python 패키지를 설치하려면 경로에 Python이 있는 명령 프롬프트를 엽니다. 다음 명령을 실행합니다.

```
pip install azure-common
pip install azure-mgmt-kusto
```

[!INCLUDE [data-explorer-authentication](includes/data-explorer-authentication.md)]

## <a name="add-a-database-principal"></a>데이터베이스 주체 추가

다음 예제에서는 데이터베이스 주체를 프로그래밍 방식으로 추가하는 방법을 보여 주며,

```Python
from azure.mgmt.kusto import KustoManagementClient
from azure.mgmt.kusto.models import DatabasePrincipalAssignment
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
#The cluster and database that is created as part of the Prerequisites
cluster_name = "mykustocluster"
database_name = "mykustodatabase"
principal_assignment_name = "clusterPrincipalAssignment1"
#User email, application ID, or security group name
principal_id = "xxxxxxxx"
#AllDatabasesAdmin or AllDatabasesViewer
role = "AllDatabasesAdmin"
tenant_id_for_principal = tenantId
#User, App, or Group
principal_type = "App"

#Returns an instance of LROPoller, check https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
poller = kusto_management_client.database_principal_assignments.create_or_update(resource_group_name=resource_group_name, cluster_name=cluster_name, database_name=database_name, principal_assignment_name= principal_assignment_name, parameters=DatabasePrincipalAssignment(principal_id=principal_id, role=role, tenant_id=tenant_id_for_principal, principal_type=principal_type))
```

|**설정** | **제안 값** | **필드 설명**|
|---|---|---|
| tenant_id | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx* | 테넌트 ID 디렉터리 ID라고도 합니다.|
| subscription_id | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx* | 리소스 만들기에 사용하는 구독 ID입니다.|
| client_id | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx* | 테넌트의 리소스에 액세스할 수 있는 응용 프로그램의 클라이언트 ID입니다.|
| client_secret | *트리플 엑스 트리플 엑스 트리플 엑스 트리플 엑스* | 테넌트의 리소스에 액세스할 수 있는 응용 프로그램의 클라이언트 보안 입니다. |
| resource_group_name | *테서그 (것)에 대한* | 클러스터를 포함하는 리소스 그룹의 이름입니다.|
| cluster_name | *mykustocluster* | 클러스터의 이름입니다.|
| database_name | *mykustodatabase* | 데이터베이스의 이름입니다.|
| principal_assignment_name | *데이터베이스주체할당1* | 데이터베이스 주 리소스의 이름입니다.|
| principal_id | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx* | 사용자 전자 메일, 응용 프로그램 ID 또는 보안 그룹 이름이 될 수 있는 주 ID입니다.|
| 역할(role) | *관리자* | '관리자', '인제스터', '모니터', '사용자', '무제한 시청자', '뷰어'가 될 수 있는 데이터베이스 주체의 역할입니다.|
| tenant_id_for_principal | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx* | 보안 주체의 테넌트 ID입니다.|
| principal_type | *앱* | '사용자', '앱' 또는 '그룹'일 수 있는 보안 주체의 유형|

## <a name="next-steps"></a>다음 단계

* [Azure 데이터 탐색기 파이썬 라이브러리를 사용하여 데이터 수집](python-ingest-data.md)
