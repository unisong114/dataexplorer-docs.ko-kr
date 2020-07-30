---
title: Azure Databricks에서 Azure 데이터 탐색기에 연결
description: 이 항목에서는 Azure Databricks를 사용 하 여 Azure 데이터 탐색기에서 데이터에 액세스 하는 방법을 보여 줍니다.
author: orspod
ms.author: orspodek
ms.reviewer: maraheja
ms.service: data-explorer
ms.topic: conceptual
ms.date: 05/21/2020
ms.openlocfilehash: 74f80ef6dd05380e73215d1edb27f5117060bc58
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87350352"
---
# <a name="connect-to-azure-data-explorer-from-azure-databricks"></a>Azure Databricks에서 Azure 데이터 탐색기에 연결

[Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/what-is-azure-databricks)는 Microsoft Azure 플랫폼에 대해 최적화된 Apache Spark 기반 분석 플랫폼입니다. 이 문서에서는 Azure 데이터 탐색기에서 Azure Databricks를 사용 하 여 데이터에 액세스 하는 방법을 보여 줍니다. 디바이스 로그인 및 Azure AD(Azure Active Directory) 앱을 포함하여 Azure Data Explorer로 인증하는 방법에는 여러 가지가 있습니다.
 
## <a name="prerequisites"></a>필수 구성 요소

- [Azure 데이터 탐색기 클러스터와 데이터베이스를 만듭니다](create-cluster-database-portal.md).
- [Azure Databricks 작업 영역을 만듭니다](/azure/azure-databricks/quickstart-create-databricks-workspace-portal#create-an-azure-databricks-workspace). **Azure Databricks 서비스** 아래 **가격 책정 계층** 드롭다운 목록에서 **프리미엄**을 선택합니다. 이를 선택하면 Azure Databricks 비밀을 사용하여 자격 증명을 저장하고 Notebook 및 작업에서 이를 참조할 수 있습니다.

- 기본 설정을 사용 하 여 Azure Databricks에서 [클러스터를 만듭니다](https://docs.azuredatabricks.net/user-guide/clusters/create.html) .

 ## <a name="install-the-kusto-spark-connector-on-your-azure-databricks-cluster"></a>Azure Databricks 클러스터에 Kusto Spark 커넥터를 설치 합니다.

Azure Databricks 클러스터에 [spark-kusto 커넥터](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/spark-kusto-connector) 를 설치 하려면 다음을 수행 합니다.

1. Azure Databricks 작업 영역으로 이동하여 [라이브러리를 생성](https://docs.azuredatabricks.net/user-guide/libraries.html#create-a-library)합니다.
1. Maven Central에서 *spark-kusto-커넥터* 패키지를 검색 하 고, 최신 버전을 설치 하 고, 클러스터에 연결 합니다. 

## <a name="connect-to-azure-data-explorer-by-using-a-device-authentication"></a>장치 인증을 사용 하 여 Azure 데이터 탐색기에 연결

[샘플 코드](https://github.com/Azure/azure-kusto-spark/blob/master/samples/src/main/python/pyKusto.py).

## <a name="connect-to-azure-data-explorer-by-using-an-azure-ad-app"></a>Azure AD 앱을 사용 하 여 Azure 데이터 탐색기에 연결

1. [Azure AD 애플리케이션을 프로비전](kusto/management/access-control/how-to-provision-aad-app.md)하여 Azure AD 앱을 만듭니다.
1. Azure Data Explorer 데이터베이스에서 Azure AD 앱에 대한 액세스 권한을 다음과 같이 부여합니다.

    ```kusto
    .set database <DB Name> users ('aadapp=<AAD App ID>;<AAD Tenant ID>') 'AAD App to connect Spark to ADX
    ```
    |   |   |
    | - | - |
    | ```DB Name``` | 데이터베이스 이름 |
    | ```AAD App ID``` | Azure AD 앱 ID |
    | ```AAD Tenant ID``` | Azure AD 테넌트 ID |

### <a name="find-your-azure-ad-tenant-id"></a>Azure AD 테넌트 ID 찾기

애플리케이션을 인증하기 위해 Azure Data Explorer는 Azure AD 테넌트 ID를 사용합니다. 테넌트 ID를 찾으려면 다음 URL을 사용하세요. *YourDomain*은 해당 도메인으로 대체하세요.

```
https://login.windows.net/<YourDomain>/.well-known/openid-configuration/
```

예를 들어 도메인이 *contoso.com*인 경우 URL은 [https://login.windows.net/contoso.com/.well-known/openid-configuration/](https://login.windows.net/contoso.com/.well-known/openid-configuration/)입니다. 결과를 보려면 이 URL을 선택하세요. 첫 번째 줄은 다음과 같습니다. 

```
"authorization_endpoint":"https://login.windows.net/6babcaad-604b-40ac-a9d7-9fd97c0b779f/oauth2/authorize"
```

테넌트 ID는 `6babcaad-604b-40ac-a9d7-9fd97c0b779f`입니다. 

### <a name="store-and-secure-your-azure-ad-app-id-and-key-optional"></a>Azure AD 앱 ID 및 키를 저장 하 고 보호 합니다 (선택 사항).  

Azure Databricks [비밀](https://docs.azuredatabricks.net/user-guide/secrets/index.html#secrets)을 사용하여 다음과 같이 Azure AD 앱 ID 및 키를 저장하고 보호합니다.

1. [CLI를 설정](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#set-up-the-cli)합니다.
1. [CLI를 설치](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#install-the-cli)합니다. 
1. [인증을 설정](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#set-up-authentication)합니다.
1. 다음 샘플 명령을 사용하여 [비밀](https://docs.azuredatabricks.net/user-guide/secrets/index.html#secrets)을 구성합니다.

    ```databricks secrets create-scope --scope adx```

    ```databricks secrets put --scope adx --key myaadappid```

    ```databricks secrets put --scope adx --key myaadappkey```

    ```databricks secrets list --scope adx```

### <a name="sample-code"></a>샘플 코드

1. [샘플 코드](https://github.com/Azure/azure-kusto-spark/blob/master/samples/src/main/python/pyKusto.py). 
1. 자리 표시자 값을 클러스터 이름, 데이터베이스 이름, 테이블 이름, Azure AD 테 넌 트 ID, AAD 앱 ID 및 AAD 앱 키로 업데이트 합니다. Databricks 암호 저장소에 자격 증명을 저장 하는 경우 해당 코드를 적절 하 게 업데이트 하 여에서 값을 검색 합니다.
