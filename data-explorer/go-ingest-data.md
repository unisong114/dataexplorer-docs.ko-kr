---
title: Azure 데이터 탐색기 Go SDK를 사용 하 여 데이터 수집
description: 이 문서에서는 Go SDK를 사용 하 여 Azure 데이터 탐색기에 데이터를 수집 (로드) 하는 방법에 대해 알아봅니다.
author: abhirockzz
ms.author: abhishgu
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/10/2020
ms.openlocfilehash: 38063f3e00ebd22da17d48abba1dd9b3510273ec
ms.sourcegitcommit: bcd0c96b1581e43e33aa35f4d68af6dcb4979d39
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/10/2020
ms.locfileid: "88039737"
---
# <a name="ingest-data-using-the-azure-data-explorer-go-sdk"></a>Azure 데이터 탐색기 Go SDK를 사용 하 여 데이터 수집 

> [!div class="op_single_selector"]
> * [.NET](net-sdk-ingest-data.md)
> * [Python](python-ingest-data.md)
> * [Node](node-ingest-data.md)
> * [Go](go-ingest-data.md)

Azure 데이터 탐색기는 로그 및 원격 분석 데이터에 사용 가능한 빠르고 확장성이 우수한 데이터 탐색 서비스입니다. Azure 데이터 탐색기 서비스와 상호 작용 하기 위한 [GO SDK 클라이언트 라이브러리](kusto/api/golang/kusto-golang-client-library.md) 를 제공 합니다. [GO SDK](https://github.com/Azure/azure-kusto-go) 를 사용 하 여 Azure 데이터 탐색기 클러스터에서 데이터를 수집, 제어 및 쿼리할 수 있습니다. 

이 문서에서는 먼저 테스트 클러스터에서 테이블 및 데이터 매핑을 만듭니다. 그런 다음 Go SDK를 사용 하 여 클러스터에 대 한 수집을 큐에 대기 하 고 결과의 유효성을 검사 합니다.

## <a name="prerequisites"></a>필수 구성 요소

* Azure 구독이 아직 없는 경우 시작하기 전에 [Azure 체험 계정](https://azure.microsoft.com/free/)을 만듭니다.
* [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)를 설치합니다.
* 다음으로 [이동](https://golang.org/) [SDK 최소 요구 사항을](kusto/api/golang/kusto-golang-client-library.md#minimum-requirements)설치 합니다. 
* [Azure 데이터 탐색기 클러스터와 데이터베이스](create-cluster-database-portal.md)를 만듭니다.

## <a name="install-the-go-sdk"></a>Go SDK 설치

Azure 데이터 탐색기 Go SDK는 [ [Go 모듈](https://golang.org/ref/mod)을 사용 하는 샘플 응용 프로그램을 실행할 때 자동으로 설치 됩니다. 다른 응용 프로그램에 대 한 Go SDK를 설치한 경우 Go 모듈을 만들고를 사용 하 여 Azure 데이터 탐색기 패키지를 가져옵니다 `go get` . 예를 들면 다음과 같습니다.

```console
go mod init foo.com/bar
go get github.com/Azure/azure-kusto-go/kusto
```

패키지 종속성이 파일에 추가 됩니다 `go.mod` . Go 응용 프로그램에서 사용 합니다.

## <a name="review-the-code"></a>코드 검토

이 [검토 코드](#review-the-code) 섹션은 선택 사항입니다. 코드가 작동 하는 방식을 알아보려면 다음 코드 조각을 검토할 수 있습니다. 그렇지 않다면 [애플리케이션 실행](#run-the-application)으로 건너뛰어도 됩니다.

### <a name="authenticate"></a>Authenticate

프로그램은 작업을 실행 하기 전에 Azure 데이터 탐색기 서비스에 인증 해야 합니다.

```go
auth := kusto.Authorization{Config: auth.NewClientCredentialsConfig(clientID, clientSecret, tenantID)}
client, err := kusto.New(kustoEndpoint, auth)
```

[Kusto의 인스턴스입니다. ](https://godoc.org/github.com/Azure/azure-kusto-go/kusto#Authorization)서비스 주체 자격 증명을 사용 하 여 권한 부여를 만듭니다. 그런 다음 kusto를 만드는 데 사용 [됩니다. ](https://godoc.org/github.com/Azure/azure-kusto-go/kusto#Client)클러스터 끝점을 허용 하는 [새](https://godoc.org/github.com/Azure/azure-kusto-go/kusto#New]) 함수를 포함 하는 클라이언트입니다.

### <a name="create-table"></a>테이블 만들기

Create table 명령은 [Kusto 문으로](https://godoc.org/github.com/Azure/azure-kusto-go/kusto#Stmt)표현 됩니다. [Mgmt](https://godoc.org/github.com/Azure/azure-kusto-go/kusto#Client.Mgmt) 함수는 관리 명령을 실행 하는 데 사용 됩니다. 명령을 실행 하 여 테이블을 만드는 데 사용 됩니다. 

```go
func createTable(kc *kusto.Client, kustoDB string) {
    _, err := kc.Mgmt(context.Background(), kustoDB, kusto.NewStmt(createTableCommand))
    if err != nil {
        log.Fatal("failed to create table", err)
    }
    log.Printf("Table %s created in DB %s\n", kustoTable, kustoDB)
}
```

> [!TIP]
> Kusto 문은 보안을 강화 하기 위해 기본적으로 상수입니다. [`NewStmt`](https://godoc.org/github.com/Azure/azure-kusto-go/kusto#NewStmt)문자열 상수를 허용 합니다. [`UnsafeStmt`](https://godoc.org/github.com/Azure/azure-kusto-go/kusto#UnsafeStmt)API는 비상수 문 세그먼트를 사용할 수 있지만 권장 되지는 않습니다.

Kusto create table 명령은 다음과 같습니다.

```kusto
.create table StormEvents (StartTime: datetime, EndTime: datetime, EpisodeId: int, EventId: int, State: string, EventType: string, InjuriesDirect: int, InjuriesIndirect: int, DeathsDirect: int, DeathsIndirect: int, DamageProperty: int, DamageCrops: int, Source: string, BeginLocation: string, EndLocation: string, BeginLat: real, BeginLon: real, EndLat: real, EndLon: real, EpisodeNarrative: string, EventNarrative: string, StormSummary: dynamic)
```

### <a name="create-mapping"></a>매핑 만들기

데이터 매핑은 수집 중에 들어오는 데이터를 Azure 데이터 탐색기 테이블 내의 열에 매핑하기 위해 사용 됩니다. 자세한 내용은 [데이터 매핑](kusto/management/mappings.md)을 참조 하세요. `Mgmt`데이터베이스 이름과 적절 한 명령과 함께 함수를 사용 하 여 테이블과 동일한 방식으로 매핑을 만들 수 있습니다. 전체 명령은 [샘플에 대 한 GitHub](https://github.com/Azure-Samples/Azure-Data-Explorer-Go-SDK-example-to-ingest-data/blob/main/main.go#L20)리포지토리에서 사용할 수 있습니다.

```go
func createMapping(kc *kusto.Client, kustoDB string) {
    _, err := kc.Mgmt(context.Background(), kustoDB, kusto.NewStmt(createMappingCommand))
    if err != nil {
        log.Fatal("failed to create mapping - ", err)
    }
    log.Printf("Mapping %s created\n", kustoMappingRefName)
}
```

### <a name="ingest-data"></a>데이터 수집

수집은 기존 Azure Blob Storage 컨테이너의 파일을 사용 하 여 큐에 대기 됩니다. 

```go
func ingestFile(kc *kusto.Client, blobStoreAccountName, blobStoreContainer, blobStoreToken, blobStoreFileName, kustoMappingRefName, kustoDB, kustoTable string) {
    kIngest, err := ingest.New(kc, kustoDB, kustoTable)
    if err != nil {
        log.Fatal("failed to create ingestion client", err)
    }
    blobStorePath := fmt.Sprintf(blobStorePathFormat, blobStoreAccountName, blobStoreContainer, blobStoreFileName, blobStoreToken)
    err = kIngest.FromFile(context.Background(), blobStorePath, ingest.FileFormat(ingest.CSV), ingest.IngestionMappingRef(kustoMappingRefName, ingest.CSV))

    if err != nil {
        log.Fatal("failed to ingest file", err)
    }
    log.Println("Ingested file from -", blobStorePath)
}
```

수집 [클라이언트는](https://godoc.org/github.com/Azure/azure-kusto-go/kusto/ingest#Ingestion) 수집을 사용 하 여 생성 됩니다 [. 새](https://godoc.org/github.com/Azure/azure-kusto-go/kusto/ingest#New)입니다. [Fromfile](https://godoc.org/github.com/Azure/azure-kusto-go/kusto/ingest#Ingestion.FromFile) 함수는 Azure Blob Storage URI를 참조 하는 데 사용 됩니다. 매핑 참조 이름과 데이터 형식은 [Fileoption](https://godoc.org/github.com/Azure/azure-kusto-go/kusto/ingest#FileOption)형식으로 전달 됩니다. 

## <a name="run-the-application"></a>애플리케이션 실행

1. GitHub에서 샘플 코드를 복제 합니다.

    ```console
    git clone https://github.com/Azure-Samples/Azure-Data-Explorer-Go-SDK-example-to-ingest-data.git
    cd Azure-Data-Explorer-Go-SDK-example-to-ingest-data
    ```

1. 다음에서이 코드 조각에 표시 된 샘플 코드를 실행 합니다 `main.go` . 

    ```go
    func main {
        ...
        dropTable(kc, kustoDB)
        createTable(kc, kustoDB)
        createMapping(kc, kustoDB)
        ingestFile(kc, blobStoreAccountName, blobStoreContainer, blobStoreToken, blobStoreFileName, kustoMappingRefName, kustoDB, kustoTable)
        ...
    }
    ```

    > [!TIP]
    > 다른 작업 조합을 시도 하려면에서 각 함수에 대 한 주석 처리를 제거 하거나 주석 처리를 수행할 수 있습니다 `main.go` .

    예제 코드를 실행 하면 다음 작업이 수행 됩니다.
    
    1. **Drop table**: `StormEvents` 테이블이 있는 경우 삭제 됩니다.
    1. **테이블 만들기**: `StormEvents` 테이블이 생성 됩니다.
    1. **매핑 만들기**: `StormEvents_CSV_Mapping` 매핑이 만들어집니다.
    1. **파일**수집: CSV 파일 (Azure Blob Storage)은 수집을 위해 큐에 대기 됩니다.

1. 인증을 위한 서비스 주체를 만들려면 [az ad sp create-rbac](https://docs.microsoft.com/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) 명령과 함께 Azure CLI를 사용 합니다. 프로그램에서 사용 되는 환경 변수 형식으로 클러스터 엔드포인트와 데이터베이스 이름을 사용 하 여 서비스 주체 정보를 설정 합니다.

    ```console
    export AZURE_SP_CLIENT_ID="<replace with appID>"
    export AZURE_SP_CLIENT_SECRET="<replace with password>"
    export AZURE_SP_TENANT_ID="<replace with tenant>"
    export KUSTO_ENDPOINT="https://<cluster name>.<azure region>.kusto.windows.net"
    export KUSTO_DB="name of the database"
    ```

1. 다음과 같이 프로그램을 실행합니다.

    ```console
    go run main.go
    ```

    유사한 출력을 얻게 됩니다.

    ```console
    Connected to Azure Data Explorer
    Using database - testkustodb
    Failed to drop StormEvents table. Maybe it does not exist?
    Table StormEvents created in DB testkustodb
    Mapping StormEvents_CSV_Mapping created
    Ingested file from - https://kustosamplefiles.blob.core.windows.net/samplefiles/StormEvents.csv?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D
    ```

## <a name="validate-and-troubleshoot"></a>유효성 검사 및 문제 해결

수집 프로세스를 예약 하 고 데이터를 Azure 데이터 탐색기에 로드 하려면 대기 중인 수집에 대해 5 ~ 10 분 동안 기다립니다. 

1. 에 로그인 하 [https://dataexplorer.azure.com](https://dataexplorer.azure.com) 고 클러스터에 연결 합니다. 그런 후 다음 명령을 실행 하 여 테이블의 레코드 수를 가져옵니다 `StormEvents` .

    ```kusto
    StormEvents | count
    ```

2. 데이터베이스에서 다음 명령을 실행하여 지난 4시간 동안 수집 실패가 있었는지 확인합니다. 실행하기 전에 데이터베이스 이름을 바꿉니다.

    ```kusto
    .show ingestion failures
    | where FailedOn > ago(4h) and Database == "<DatabaseName>"
    ```

1. 다음 명령을 실행하여 지난 4시간 동안 진행된 모든 수집 작업의 상태를 확인합니다. 실행하기 전에 데이터베이스 이름을 바꿉니다.

    ```kusto
    .show operations
    | where StartedOn > ago(4h) and Database == "<DatabaseName>" and Operation == "DataIngestPull"
    | summarize arg_max(LastUpdatedOn, *) by OperationId
    ```

## <a name="clean-up-resources"></a>리소스 정리

다른 문서를 따르려면 만든 리소스를 유지 합니다. 그렇지 않은 경우 데이터베이스에서 다음 명령을 실행 하 여 테이블을 삭제 `StormEvents` 합니다.

```kusto
.drop table StormEvents
```

## <a name="next-steps"></a>다음 단계

[쿼리 작성](write-queries.md)
