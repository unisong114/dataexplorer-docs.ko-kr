---
title: Azure Go SDK를 사용 하 여 Azure 데이터 탐색기 클러스터 & 데이터베이스 만들기
description: Azure Go SDK를 사용 하 여 Azure 데이터 탐색기 클러스터 및 데이터베이스를 만들고, 나열 하 고, 삭제 하는 방법에 대해 알아봅니다.
author: orspod
ms.author: orspodek
ms.reviewer: abhishgu
ms.service: data-explorer
ms.topic: how-to
ms.date: 10/28/2020
ms.openlocfilehash: 833a801e6455fd4d88fbbbab83010aea1d406f02
ms.sourcegitcommit: 7edce9d9d20f9c0505abda67bb8cc3d2ecd60d15
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2020
ms.locfileid: "96524252"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-using-go"></a>Go를 사용 하 여 Azure 데이터 탐색기 클러스터 및 데이터베이스 만들기

> [!div class="op_single_selector"]
> * [포털](create-cluster-database-portal.md)
> * [CLI](create-cluster-database-cli.md)
> * [PowerShell](create-cluster-database-powershell.md)
> * [C#](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
> * [Go](create-cluster-database-go.md)
> * [ARM 템플릿](create-cluster-database-resource-manager.md)

Azure Data Explorer는 애플리케이션, 웹 사이트, IoT 디바이스 등으로부터 대량의 데이터 스트리밍에 대한 실시간 분석을 제공하는 빠른 속도의 완전 관리형 데이터 분석 서비스입니다. Azure Data Explorer를 사용하려면 먼저 클러스터를 만들고 이 클러스터에 데이터베이스를 하나 이상 만듭니다. 그런 다음, 데이터베이스에 대해 쿼리를 실행할 수 있도록 데이터베이스에 데이터를 수집(로드)합니다. 

이 문서에서는 [Go](https://golang.org/)를 사용 하 여 Azure 데이터 탐색기 클러스터와 데이터베이스를 만듭니다. 그런 다음 새 클러스터와 데이터베이스를 나열 하 고 삭제 하 고 리소스에 대 한 작업을 실행할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독이 아직 없는 경우 시작하기 전에 [Azure 체험 계정](https://azure.microsoft.com/free)을 만듭니다.
* [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)를 설치합니다.
* Go의 적절 한 버전을 설치 합니다. 지원 되는 릴리스에 대 한 자세한 내용은 [Azure GO SDK](https://github.com/Azure/azure-sdk-for-go)를 참조 하세요.

## <a name="review-the-code"></a>코드 검토

이 섹션은 선택 사항입니다. 코드가 작동 하는 방식을 알아보려면 다음 코드 조각을 검토할 수 있습니다. 그렇지 않다면 [애플리케이션 실행](#run-the-application)으로 건너뛰어도 됩니다.

### <a name="authentication"></a>인증

프로그램은 작업을 실행 하기 전에 Azure 데이터 탐색기에 인증 해야 합니다. [클라이언트 자격 증명 인증 유형은](/azure/developer/go/azure-sdk-authorization#use-environment-based-authentication) 인증에 사용 됩니다 [. ](https://pkg.go.dev/github.com/Azure/go-autorest/autorest/azure/auth?tab=doc#NewAuthorizerFromEnvironment)미리 정의 된 환경 변수 `AZURE_CLIENT_ID` , `AZURE_CLIENT_SECRET` , `AZURE_TENANT_ID` 를 검색 하는 NewAuthorizerFromEnvironment입니다.

다음 예에서는 [kusto를 보여 줍니다. ](https://pkg.go.dev/github.com/Azure/azure-sdk-for-go@v48.2.0+incompatible/services/kusto/mgmt/2020-02-15/kusto) 이 기술을 사용 하 여 ClustersClient를 만듭니다.

```go
func getClustersClient(subscription string) kusto.ClustersClient {
    client := kusto.NewClustersClient(subscription)
    authR, err := auth.NewAuthorizerFromEnvironment()
    if err != nil {
        log.Fatal(err)
    }
    client.Authorizer = authR

    return client
}
```

> [!TIP]
> Auth를 사용 [합니다. ](https://pkg.go.dev/github.com/Azure/go-autorest/autorest/azure/auth?tab=doc#NewAuthorizerFromCLIWithResource) Azure CLI 설치 되 고 인증을 위해 구성 된 경우 로컬 개발을 위한 NewAuthorizerFromCLIWithResource 함수입니다.

### <a name="create-cluster"></a>클러스터 만들기

에서 [Createorupdate](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/kusto/mgmt/2018-09-07-preview/kusto) 함수를 사용 `kusto.ClustersClient` 하 여 새 Azure 데이터 탐색기 클러스터를 만듭니다. 결과를 검사 하기 전에 프로세스가 완료 될 때까지 기다립니다.

```go
func createCluster(sub, name, location, rgName string) {
    ...
    result, err := client.CreateOrUpdate(ctx, rgName, name, kusto.Cluster{Location: &location, Sku: &kusto.AzureSku{Name: kusto.DevNoSLAStandardD11V2, Capacity: &numInstances, Tier: kusto.Basic}})
    ...
    err = result.WaitForCompletionRef(context.Background(), client.Client)
    ...
    r, err := result.Result(client)
}
```

### <a name="list-clusters"></a>클러스터 나열

에서 [Listbyresourcegroup](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/kusto/mgmt/2018-09-07-preview/kusto#ClustersClient.ListByResourceGroup) 함수를 사용 `kusto.ClustersClient` 하 여 [kusto를 가져옵니다. ](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/kusto/mgmt/2018-09-07-preview/kusto#ClusterListResult) 그런 다음 출력을 테이블 형식으로 표시 하기 위해 반복 되는 ClusterListResult입니다.


```go
func listClusters(sub, rgName string) {
    ...
    result, err := getClustersClient(sub).ListByResourceGroup(ctx, rgName)
    ...
    for _, c := range *result.Value {
        // setup tabular representation
    }
    ...
}
```

### <a name="create-database"></a>데이터베이스 만들기

Kusto에서 [Createorupdate](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/kusto/mgmt/2018-09-07-preview/kusto#DatabasesClient.CreateOrUpdate) 함수를 사용 [합니다. DatabasesClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/kusto/mgmt/2018-09-07-preview/kusto#DatabasesClient) 를 통해 기존 클러스터에 새 Azure 데이터 탐색기 데이터베이스를 만들 수 있습니다. 결과를 검사 하기 전에 프로세스가 완료 될 때까지 기다립니다.


```go
func createDatabase(sub, rgName, clusterName, location, dbName string) {
    future, err := client.CreateOrUpdate(ctx, rgName, clusterName, dbName, kusto.ReadWriteDatabase{Kind: kusto.KindReadWrite, Location: &location})
    ...
    err = future.WaitForCompletionRef(context.Background(), client.Client)
    ...
    r, err := future.Result(client)
    ...
}
```

### <a name="list-databases"></a>데이터베이스 나열

에서 [Listbycluster](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/kusto/mgmt/2018-09-07-preview/kusto#DatabasesClient.ListByCluster) 함수를 사용 `kusto.DatabasesClient` 하 여 [kusto를 가져옵니다. ](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/kusto/mgmt/2018-09-07-preview/kusto#DatabaseListResult) 테이블 형식으로 출력을 표시 하기 위해 반복 되는 DatabaseListResult입니다.


```go
func listDatabases(sub, rgName, clusterName string) {
    result, err := getDBClient(sub).ListByCluster(ctx, rgName, clusterName)
    ...
    for _, db := range *result.Value {
        // setup tabular representation
    }
    ...
}
```

### <a name="delete-database"></a>데이터베이스 삭제

에서 [delete](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/kusto/mgmt/2018-09-07-preview/kusto#DatabasesClient.Delete) 함수를 사용 `kusto.DatabasesClient` 하 여 클러스터에서 기존 데이터베이스를 삭제 합니다. 결과를 검사 하기 전에 프로세스가 완료 될 때까지 기다립니다.

```go
func deleteDatabase(sub, rgName, clusterName, dbName string) {
    ...
    future, err := getDBClient(sub).Delete(ctx, rgName, clusterName, dbName)
    ...
    err = future.WaitForCompletionRef(context.Background(), client.Client)
    ...
    r, err := future.Result(client)
    if r.StatusCode == 200 {
        // determine success or failure
    }
    ...
}
```

### <a name="delete-cluster"></a>클러스터 삭제

에서 [delete](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/kusto/mgmt/2018-09-07-preview/kusto#ClustersClient.Delete) 함수를 사용 `kusto.ClustersClient` 하 여 클러스터를 삭제 합니다. 결과를 검사 하기 전에 프로세스가 완료 될 때까지 기다립니다.

```go
func deleteCluster(sub, clusterName, rgName string) {
    result, err := client.Delete(ctx, rgName, clusterName)
    ...
    err = result.WaitForCompletionRef(context.Background(), client.Client)
    ...
    r, err := result.Result(client)
    if r.StatusCode == 200 {
        // determine success or failure
    }
    ...
}
```

## <a name="run-the-application"></a>애플리케이션 실행

샘플 코드를 있는 그대로 실행 하면 다음 작업이 수행 됩니다.
    
1. Azure 데이터 탐색기 클러스터가 만들어집니다.
1. 지정 된 리소스 그룹의 모든 Azure 데이터 탐색기 클러스터가 나열 됩니다.
1. Azure 데이터 탐색기 데이터베이스가 기존 클러스터에 만들어집니다.
1. 지정 된 클러스터의 모든 데이터베이스가 나열 됩니다.
1. 데이터베이스가 삭제 됩니다.
1. 클러스터가 삭제 됩니다.


    ```go
    func main() {
        createCluster(subscription, clusterNamePrefix+clusterName, location, rgName)
        listClusters(subscription, rgName)
        createDatabase(subscription, rgName, clusterNamePrefix+clusterName, location, dbNamePrefix+databaseName)
        listDatabases(subscription, rgName, clusterNamePrefix+clusterName)
        deleteDatabase(subscription, rgName, clusterNamePrefix+clusterName, dbNamePrefix+databaseName)
        deleteCluster(subscription, clusterNamePrefix+clusterName, rgName)
    }
    ```

    > [!TIP]
    > 작업의 여러 조합을 시도 하려면 필요에 따라의 각 함수에 대 한 주석 처리를 제거 하 고 주석 처리를 수행할 수 있습니다 `main.go` .

1. GitHub에서 샘플 코드를 복제 합니다.

    ```console
    git clone https://github.com/Azure-Samples/azure-data-explorer-go-cluster-management.git
    cd azure-data-explorer-go-cluster-management
    ```

1. 프로그램은 클라이언트 자격 증명을 사용 하 여 인증 합니다. Azure CLI [az ad sp create-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) 명령을 사용 하 여 서비스 주체를 만듭니다. 다음 단계에서 사용할 클라이언트 ID, 클라이언트 암호 및 테 넌 트 ID 정보를 저장 합니다.

1. 서비스 사용자 정보를 포함 하 여 필요한 환경 변수를 내보냅니다. 구독 ID, 리소스 그룹 및 클러스터를 만들려는 지역을 입력 합니다.

    ```console
    export AZURE_CLIENT_ID="<enter service principal client ID>"
    export AZURE_CLIENT_SECRET="<enter service principal client secret>"
    export AZURE_TENANT_ID="<enter tenant ID>"

    export SUBSCRIPTION="<enter subscription ID>"
    export RESOURCE_GROUP="<enter resource group name>"
    export LOCATION="<enter azure location e.g. Southeast Asia>"

    export CLUSTER_NAME_PREFIX="<enter prefix (cluster name will be [prefix]-ADXTestCluster)>"
    export DATABASE_NAME_PREFIX="<enter prefix (database name will be [prefix]-ADXTestDB)>"
    ```
    
    > [!TIP]
    > 프로덕션 시나리오에서 환경 변수를 사용 하 여 응용 프로그램에 자격 증명을 제공할 수 있습니다. [코드 검토](#review-the-code)에 설명 된 대로 로컬 개발의 경우 auth를 사용 [합니다. ](https://pkg.go.dev/github.com/Azure/go-autorest/autorest/azure/auth?tab=doc#NewAuthorizerFromCLIWithResource)Azure CLI 설치 되 고 인증을 위해 구성 된 경우에는 NewAuthorizerFromCLIWithResource 합니다. 이 경우 서비스 주체를 만들 필요가 없습니다.


1. 다음과 같이 프로그램을 실행합니다.

    ```console
    go run main.go
    ```

    다음과 유사한 출력이 표시 됩니다.

    ```console
    waiting for cluster creation to complete - fooADXTestCluster
    created cluster fooADXTestCluster
    listing clusters in resource group <your resource group>
    +-------------------+---------+----------------+-----------+-----------------------------------------------------------+
    |       NAME        |  STATE  |    LOCATION    | INSTANCES |                            URI                           |
    +-------------------+---------+----------------+-----------+-----------------------------------------------------------+
    | fooADXTestCluster | Running | Southeast Asia |         1 | https://fooADXTestCluster.southeastasia.kusto.windows.net |
    +-------------------+---------+----------------+-----------+-----------------------------------------------------------+
    
    waiting for database creation to complete - barADXTestDB
    created DB fooADXTestCluster/barADXTestDB with ID /subscriptions/<your subscription ID>/resourceGroups/<your resource group>/providers/Microsoft.Kusto/Clusters/fooADXTestCluster/Databases/barADXTestDB and type Microsoft.Kusto/Clusters/Databases
    
    listing databases in cluster fooADXTestCluster
    +--------------------------------+-----------+----------------+------------------------------------+
    |              NAME              |   STATE   |    LOCATION    |                TYPE                |
    +--------------------------------+-----------+----------------+------------------------------------+
    | fooADXTestCluster/barADXTestDB | Succeeded | Southeast Asia | Microsoft.Kusto/Clusters/Databases |
    +--------------------------------+-----------+----------------+------------------------------------+
    
    waiting for database deletion to complete - barADXTestDB
    deleted DB barADXTestDB from cluster fooADXTestCluster

    waiting for cluster deletion to complete - fooADXTestCluster
    deleted ADX cluster fooADXTestCluster from resource group <your resource group>
    ```

## <a name="clean-up-resources"></a>리소스 정리

이 문서의 샘플 코드를 사용 하 여 프로그래밍 방식으로 클러스터를 삭제 하지 않은 경우 [Azure CLI](create-cluster-database-cli.md#clean-up-resources)를 사용 하 여 수동으로 삭제 하세요.

## <a name="next-steps"></a>다음 단계

[Azure 데이터 탐색기 Go SDK를 사용 하 여 데이터 수집](go-ingest-data.md)
