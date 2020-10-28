---
title: Azure 데이터 탐색기 Java SDK를 사용 하 여 데이터 수집
description: 이 문서에서는 Java SDK를 사용 하 여 Azure 데이터 탐색기에 데이터를 수집 (로드) 하는 방법에 대해 알아봅니다.
author: orspod
ms.author: orspodek
ms.reviewer: abhishgu
ms.service: data-explorer
ms.topic: how-to
ms.date: 10/22/2020
ms.openlocfilehash: 45b0ce7b1716a4ed2ab7277b6c99a5d95f8ad5af
ms.sourcegitcommit: a7458819e42815a0376182c610aba48519501d92
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/28/2020
ms.locfileid: "92906314"
---
# <a name="ingest-data-using-the-azure-data-explorer-java-sdk"></a>Azure 데이터 탐색기 Java SDK를 사용 하 여 데이터 수집 

> [!div class="op_single_selector"]
> * [.NET](net-sdk-ingest-data.md)
> * [Python](python-ingest-data.md)
> * [Node](node-ingest-data.md)
> * [Go](go-ingest-data.md)
> * [Java](java-ingest-data.md)

Azure 데이터 탐색기는 로그 및 원격 분석 데이터에 사용 가능한 빠르고 확장성이 우수한 데이터 탐색 서비스입니다. [Java 클라이언트 라이브러리](kusto/api/java/kusto-java-client-library.md) 를 사용 하 여 Azure 데이터 탐색기 클러스터에서 데이터를 수집 하 고, 제어 명령을 실행 하 고, 데이터를 쿼리할 수 있습니다.

이 문서에서는 Azure 데이터 탐색기 Java 라이브러리를 사용 하 여 데이터를 수집 하는 방법을 알아봅니다. 먼저 테스트 클러스터에서 테이블과 데이터 매핑을 만듭니다. 그런 다음 Java SDK를 사용 하 여 blob 저장소에서 클러스터로 수집을 큐에 대기 하 고 결과의 유효성을 검사 합니다.

## <a name="prerequisites"></a>사전 요구 사항

* [무료 Azure 계정](https://azure.microsoft.com/free/).
* [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)
* JDK 버전 1.8 이상
* [Maven](https://maven.apache.org/download.cgi)
* [클러스터 및 데이터베이스](create-cluster-database-portal.md)
* [앱 등록을 만들고 데이터베이스에 대 한 권한을 부여](provision-azure-ad-app.md)합니다. 나중에 사용 하기 위해 클라이언트 ID 및 클라이언트 암호를 저장 합니다.

## <a name="review-the-code"></a>코드 검토

이 섹션은 선택 사항입니다. 다음 코드 조각을 검토 하 여 코드가 작동 하는 방법을 알아봅니다. 이 섹션을 건너뛰려면 [응용 프로그램 실행](#run-the-application)으로 이동 합니다.

### <a name="authentication"></a>인증

프로그램은 ConnectionStringBuilder '와 Azure Active Directory 인증 자격 증명을 사용 합니다.

1. `com.microsoft.azure.kusto.data.Client`쿼리 및 관리를 위해를 만듭니다.

    ```java
    static Client getClient() throws Exception {
        ConnectionStringBuilder csb = ConnectionStringBuilder.createWithAadApplicationCredentials(endpoint, clientID, clientSecret, tenantID);
        return ClientFactory.createClient(csb);
    }
    ```

1. 를 만들고 사용 `com.microsoft.azure.kusto.ingest.IngestClient` 하 여 데이터 수집을 Azure 데이터 탐색기에 대기 합니다.

    ```java
    static IngestClient getIngestionClient() throws Exception {
        String ingestionEndpoint = "https://ingest-" + URI.create(endpoint).getHost();
        ConnectionStringBuilder csb = ConnectionStringBuilder.createWithAadApplicationCredentials(ingestionEndpoint, clientID, clientSecret);
        return IngestClientFactory.createClient(csb);
    }
    ```

### <a name="management-commands"></a>관리 명령

및와 같은 [제어 명령은](kusto/management/commands.md) [`.drop`](kusto/management/drop-function.md) [`.create`](kusto/management/create-function.md) `execute` 개체에 대해를 호출 하 여 실행 됩니다 `com.microsoft.azure.kusto.data.Client` .

예를 들어 테이블은 다음과 `StormEvents` 같이 생성 됩니다.

```java
static final String createTableCommand = ".create table StormEvents (StartTime: datetime, EndTime: datetime, EpisodeId: int, EventId: int, State: string, EventType: string, InjuriesDirect: int, InjuriesIndirect: int, DeathsDirect: int, DeathsIndirect: int, DamageProperty: int, DamageCrops: int, Source: string, BeginLocation: string, EndLocation: string, BeginLat: real, BeginLon: real, EndLat: real, EndLon: real, EpisodeNarrative: string, EventNarrative: string, StormSummary: dynamic)";

static void createTable(String database) {
    try {
        getClient().execute(database, createTableCommand);
        System.out.println("Table created");
    } catch (Exception e) {
        System.out.println("Failed to create table: " + e.getMessage());
        return;
    }
    
}
```

### <a name="data-ingestion"></a>데이터 수집

기존 Azure Blob Storage 컨테이너의 파일을 사용 하 여 큐에 수집 합니다. 
* `BlobSourceInfo`Blob Storage 경로를 지정 하는 데 사용 합니다.
* `IngestionProperties`테이블, 데이터베이스, 매핑 이름 및 데이터 형식을 정의 하는 데 사용 합니다. 다음 예제에서 데이터 형식은 `CSV` 입니다.

```java
    ...
    static final String blobPathFormat = "https://%s.blob.core.windows.net/%s/%s%s";
    static final String blobStorageAccountName = "kustosamplefiles";
    static final String blobStorageContainer = "samplefiles";
    static final String fileName = "StormEvents.csv";
    static final String blobStorageToken = "??st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D";
    ....

    static void ingestFile(String database) throws InterruptedException {
        String blobPath = String.format(blobPathFormat, blobStorageAccountName, blobStorageContainer,
                fileName, blobStorageToken);
        BlobSourceInfo blobSourceInfo = new BlobSourceInfo(blobPath);

        IngestionProperties ingestionProperties = new IngestionProperties(database, tableName);
        ingestionProperties.setDataFormat(DATA_FORMAT.csv);
        ingestionProperties.setIngestionMapping(ingestionMappingRefName, IngestionMappingKind.Csv);
        ingestionProperties.setReportLevel(IngestionReportLevel.FailuresAndSuccesses);
        ingestionProperties.setReportMethod(IngestionReportMethod.QueueAndTable);
    ....
```

수집 프로세스는 별도의 스레드에서 시작 되 고 스레드는 수집 `main` 스레드가 완료 될 때까지 대기 합니다. 이 프로세스에서는 [Countdownlatch](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/CountDownLatch.html)를 사용 합니다. 수집 API ( `IngestClient#ingestFromBlob` )는 비동기가 아닙니다. `while`루프는 5 초 마다 현재 상태를 폴링하고 수집 상태가에서 다른 상태로 변경 될 때까지 대기 하는 데 사용 됩니다 `Pending` . 최종 상태는, 또는 일 수 있습니다 `Succeeded` `Failed` `PartiallySucceeded` .

```java
        ....
        CountDownLatch ingestionLatch = new CountDownLatch(1);
        new Thread(new Runnable() {
            @Override
            public void run() {
                IngestionResult result = null;
                try {
                    result = getIngestionClient().ingestFromBlob(blobSourceInfo, ingestionProperties);
                } catch (Exception e) {
                    ingestionLatch.countDown();
                }
                try {
                    IngestionStatus status = result.getIngestionStatusCollection().get(0);
                    while (status.status == OperationStatus.Pending) {
                        Thread.sleep(5000);
                        status = result.getIngestionStatusCollection().get(0);
                    }
                    ingestionLatch.countDown();
                } catch (Exception e) {
                    ingestionLatch.countDown();
                }
            }
        }).start();
        ingestionLatch.await();
    }
```

> [!TIP]
> 다른 응용 프로그램에 대 한 수집을 비동기식으로 처리 하는 다른 메서드가 있습니다. 예를 들어를 사용 [`CompletableFuture`](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/CompletableFuture.html) 하 여 테이블 쿼리 등의 작업 사후 수집을 정의 하거나에 보고 된 예외를 처리 하는 파이프라인을 만들 수 있습니다 `IngestionStatus` .

## <a name="run-the-application"></a>애플리케이션 실행

### <a name="general"></a>일반

예제 코드를 실행 하면 다음 작업이 수행 됩니다.

   1. **Drop table** : `StormEvents` 테이블이 있는 경우 삭제 됩니다.
   1. **테이블 만들기** : `StormEvents` 테이블이 생성 됩니다.
   1. **매핑 만들기** : `StormEvents_CSV_Mapping` 매핑이 만들어집니다.
   1. **파일** 수집: CSV 파일 (Azure Blob Storage)은 수집을 위해 큐에 대기 됩니다.

다음 샘플 코드는에서 가져온 것입니다 `App.java` .

```java
public static void main(final String[] args) throws Exception {
    dropTable(database);
    createTable(database);
    createMapping(database);
    ingestFile(database);
}
```

> [!TIP]
> 다른 작업 조합을 시도 하려면에서 해당 메서드의 주석 처리를 제거 하거나 주석 처리를 제거 `App.java` 합니다.

### <a name="run-the-application"></a>애플리케이션 실행

1. GitHub에서 샘플 코드를 복제 합니다.

    ```console
    git clone https://github.com/Azure-Samples/azure-data-explorer-java-sdk-ingest.git
    cd azure-data-explorer-java-sdk-ingest
    ```

1. 다음 정보를 사용 하 여 서비스 사용자 정보를 프로그램에서 사용 하는 환경 변수로 설정 합니다.
    * 클러스터 끝점 
    * 데이터베이스 이름 

    ```console
    export AZURE_SP_CLIENT_ID="<replace with appID>"
    export AZURE_SP_CLIENT_SECRET="<replace with password>"
    export KUSTO_ENDPOINT="https://<cluster name>.<azure region>.kusto.windows.net"
    export KUSTO_DB="name of the database"
    ```

1. 빌드 및 실행:

    ```console
    mvn clean package
    java -jar target/adx-java-ingest-jar-with-dependencies.jar
    ```

    다음과 유사하게 출력됩니다.

    ```console
    Table dropped
    Table created
    Mapping created
    Waiting for ingestion to complete...
    ```

수집 프로세스가 완료 될 때까지 몇 분 정도 기다립니다. 성공적으로 완료 되 면 다음과 같은 로그 메시지가 표시 됩니다 `Ingestion completed successfully` . 이 시점에서 프로그램을 종료 하 고 이미 큐에 대기 된 수집 프로세스에 영향을 주지 않고 다음 단계로 이동할 수 있습니다.

## <a name="validate"></a>유효성 검사

대기 중인 수집에 대해 5 ~ 10 분 동안 대기 하 여 수집 프로세스를 예약 하 고 데이터를 Azure 데이터 탐색기로 로드 합니다. 

1. 에 로그인 하 [https://dataexplorer.azure.com](https://dataexplorer.azure.com) 고 클러스터에 연결 합니다. 
1. 다음 명령을 실행 하 여 테이블의 레코드 수를 가져옵니다 `StormEvents` .
    
    ```kusto
    StormEvents | count
    ```

## <a name="troubleshoot"></a>문제 해결

1. 지난 4 시간 동안 수집 오류를 확인 하려면 데이터베이스에서 다음 명령을 실행 합니다. 

    ```kusto
    .show ingestion failures
    | where FailedOn > ago(4h) and Database == "<DatabaseName>"
    ```

1. 지난 4 시간 동안 모든 수집 작업의 상태를 보려면 다음 명령을 실행 합니다.

    ```kusto
    .show operations
    | where StartedOn > ago(4h) and Database == "<DatabaseName>" and Operation == "DataIngestPull"
    | summarize arg_max(LastUpdatedOn, *) by OperationId
    ```

## <a name="clean-up-resources"></a>리소스 정리

방금 만든 리소스를 사용 하지 않으려면 데이터베이스에서 다음 명령을 실행 하 여 테이블을 삭제 `StormEvents` 합니다.

```kusto
.drop table StormEvents
```

## <a name="next-steps"></a>다음 단계

[쿼리 작성](write-queries.md)
