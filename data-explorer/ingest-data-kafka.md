---
title: Kafka에서 Azure Data Explorer로 데이터 수집
description: 이 문서에서는 Kafka에서 Azure 데이터 탐색기로 데이터를 수집 (로드) 하는 방법에 대해 알아봅니다.
author: orspod
ms.author: orspodek
ms.reviewer: ankhanol
ms.service: data-explorer
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: cc2f10570081fec3a5762ab3f2e23b9e22839063
ms.sourcegitcommit: c6cb2b1071048daa872e2fe5a1ac7024762c180e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/07/2020
ms.locfileid: "96774659"
---
# <a name="ingest-data-from-apache-kafka-into-azure-data-explorer"></a>Apache Kafka에서 Azure 데이터 탐색기로 데이터 수집
 
Azure 데이터 탐색기는 [Apache Kafka](http://kafka.apache.org/documentation/)에서 [데이터](ingest-data-overview.md) 수집을 지원 합니다. Apache Kafka는 시스템 또는 응용 프로그램 간에 데이터를 안정적으로 이동 하는 실시간 스트리밍 데이터 파이프라인을 빌드하기 위한 분산형 스트리밍 플랫폼입니다. [Kafka Connect](https://docs.confluent.io/3.0.1/connect/intro.html#kafka-connect) 는 Apache Kafka와 다른 데이터 시스템 간에 확장 가능 하 고 안정적으로 데이터를 스트리밍하는 도구입니다. Azure 데이터 탐색기 [Kafka Sink](https://github.com/Azure/kafka-sink-azure-kusto/blob/master/README.md) 는 kafka의 커넥터 역할을 하며 코드를 사용할 필요가 없습니다. 이 [Git 리포지토리](https://github.com/Azure/kafka-sink-azure-kusto/releases) 또는 [Confluent 커넥터 허브](https://www.confluent.io/hub/microsoftcorporation/kafka-sink-azure-kusto)에서 싱크 커넥터 jar를 다운로드 합니다.

이 문서에서는 자체 포함 Docker 설치를 사용 하 여 kafka을 Azure 데이터 탐색기에 수집 하 여 kafka 클러스터 및 Kafka 커넥터 클러스터 설정을 간소화 하는 방법을 보여 줍니다. 

자세한 내용은 [Git 리포지토리](https://github.com/Azure/kafka-sink-azure-kusto/blob/master/README.md) 및 [버전 세부](https://github.com/Azure/kafka-sink-azure-kusto/blob/master/README.md#13-major-version-specifics)사항 커넥터를 참조 하세요.

## <a name="prerequisites"></a>사전 요구 사항

* [Microsoft Azure 계정을](/azure/)만듭니다.
* [Azure CLI](/cli/azure/install-azure-cli)를 설치합니다.
* [Docker](https://docs.docker.com/get-docker/) 를 설치 하 고 [Docker Compose](https://docs.docker.com/compose/install)합니다.
* 기본 캐시 및 보존 정책을 사용 하 여 [Azure Portal에서 Azure 데이터 탐색기 클러스터와 데이터베이스를 만듭니다](create-cluster-database-portal.md) .

## <a name="create-an-azure-active-directory-service-principal"></a>Azure Active Directory 서비스 사용자 만들기

Azure Active Directory 서비스 주체는 다음 예제와 같이 [Azure Portal](/azure/active-directory/develop/howto-create-service-principal-portal) 를 통해 또는 프로그래밍 방식으로 만들 수 있습니다.

이 서비스 주체는 Azure 데이터 탐색기 테이블에 쓰기 위해 커넥터에서 활용 하는 id입니다. 나중에이 서비스 사용자가 Azure 데이터 탐색기에 액세스할 수 있는 권한을 부여 합니다.

1. Azure CLI를 통해 Azure 구독에 로그인 합니다. 그런 다음 브라우저에서 인증 합니다.

   ```azurecli-interactive
   az login
   ```


1. 랩을 실행 하는 데 사용할 구독을 선택 합니다. 여러 구독이 있는 경우이 단계가 필요 합니다.

   ```azurecli-interactive
   az account set --subscription YOUR_SUBSCRIPTION_GUID
   ```

1. 서비스 주체를 만듭니다. 이 예제에서는 서비스 사용자가 호출 됩니다 `kusto-kafka-spn` .

   ```azurecli-interactive
   az ad sp create-for-rbac -n "kusto-kafka-spn"
   ```

1. 아래와 같이 JSON 응답을 받게 됩니다. `appId` `password` `tenant` 이후 단계에서 필요 하므로, 및를 복사 합니다.

    ```json
    {
      "appId": "fe7280c7-5705-4789-b17f-71a472340429",
      "displayName": "kusto-kafka-spn",
      "name": "http://kusto-kafka-spn",
      "password": "29c719dd-f2b3-46de-b71c-4004fb6116ee",
      "tenant": "42f988bf-86f1-42af-91ab-2d7cd011db42"
    }
    ```

## <a name="create-a-target-table-in-azure-data-explorer"></a>Azure 데이터 탐색기에서 대상 테이블 만들기

1. [Azure 포털](https://portal.azure.com)

1. Azure 데이터 탐색기 클러스터로 이동 합니다.

1. `Storms`다음 명령을 사용 하 여 라는 테이블을 만듭니다.

    ```kusto
    .create table Storms (StartTime: datetime, EndTime: datetime, EventId: int, State: string, EventType: string, Source: string)
    ```

    :::image type="content" source="media/ingest-data-kafka/create-table.png" alt-text="Azure 데이터 탐색기 포털에서 테이블 만들기 ":::
    
1. `Storms_CSV_Mapping`다음 명령을 사용 하 여 수집 데이터에 대 한 해당 테이블 매핑을 만듭니다.
    
    ```kusto
    .create table Storms ingestion csv mapping 'Storms_CSV_Mapping' '[{"Name":"StartTime","datatype":"datetime","Ordinal":0}, {"Name":"EndTime","datatype":"datetime","Ordinal":1},{"Name":"EventId","datatype":"int","Ordinal":2},{"Name":"State","datatype":"string","Ordinal":3},{"Name":"EventType","datatype":"string","Ordinal":4},{"Name":"Source","datatype":"string","Ordinal":5}]'
    ```    

1. 구성 가능한 수집 대기 시간에 대 한 테이블에서 일괄 처리 수집 정책을 만듭니다.

    > [!TIP]
    > 수집 [일괄 처리 정책은](kusto/management/batchingpolicy.md) 성능 최적화 프로그램으로, 세 개의 매개 변수를 포함 합니다. 첫 번째 조건을 충족 하는 트리거는 Azure 데이터 탐색기 테이블로 수집 합니다.

    ```kusto
    .alter table Storms policy ingestionbatching @'{"MaximumBatchingTimeSpan":"00:00:15", "MaximumNumberOfItems": 100, "MaximumRawDataSizeMB": 300}'
    ```

1. [Azure Active Directory 서비스 주체 만들기](#create-an-azure-active-directory-service-principal) 의 서비스 주체를 사용 하 여 데이터베이스에 대 한 작업 권한을 부여 합니다.

    ```kusto
    .add database YOUR_DATABASE_NAME admins  ('aadapp=YOUR_APP_ID;YOUR_TENANT_ID') 'AAD App'
    ```

## <a name="run-the-lab"></a>랩 실행

다음 랩에서는 데이터 만들기, Kafka 커넥터 설정 및 커넥터를 사용 하 여이 데이터를 Azure 데이터 탐색기로 스트리밍 작업을 시작 하는 환경을 제공 하도록 설계 되었습니다. 그런 다음 수집 데이터를 살펴볼 수 있습니다.

### <a name="clone-the-git-repo"></a>Git 리포지토리를 복제 합니다.

랩의 git [리포지토리](https://github.com/Azure/azure-kusto-labs)를 복제 합니다.

1. 컴퓨터에 로컬 디렉터리를 만듭니다.

    ```
    mkdir ~/kafka-kusto-hol
    cd ~/kafka-kusto-hol
    ```

1. 리포지토리를 복제 합니다.

    ```shell
    cd ~/kafka-kusto-hol
    git clone https://github.com/Azure/azure-kusto-labs
    cd azure-kusto-labs/kafka-integration/dockerized-quickstart
    ```

#### <a name="contents-of-the-cloned-repo"></a>복제 된 리포지토리의 내용

다음 명령을 실행 하 여 복제 된 리포지토리의 콘텐츠를 나열 합니다.

```
cd ~/kafka-kusto-hol/azure-kusto-labs/kafka-integration/dockerized-quickstart
tree
```

이 검색의 결과는 다음과 같습니다.

```
├── README.md
├── adx-query.png
├── adx-sink-config.json
├── connector
│   └── Dockerfile
├── docker-compose.yaml
└── storm-events-producer
    ├── Dockerfile
    ├── StormEvents.csv
    ├── go.mod
    ├── go.sum
    ├── kafka
    │   └── kafka.go
    └── main.go
 ```

### <a name="review-the-files-in-the-cloned-repo"></a>복제 된 리포지토리의 파일 검토

다음 섹션에서는 위의 파일 트리에서 파일의 중요 한 부분에 대해 설명 합니다.

#### <a name="adx-sink-configjson"></a>adx-sink-config.js

이 파일에는 특정 구성 세부 정보를 업데이트 하는 Kusto 싱크 속성 파일이 포함 되어 있습니다.
 
```json
{
    "name": "storm",
    "config": {
        "connector.class": "com.microsoft.azure.kusto.kafka.connect.sink.KustoSinkConnector",
        "flush.size.bytes": 10000,
        "flush.interval.ms": 10000,
        "tasks.max": 1,
        "topics": "storm-events",
        "kusto.tables.topics.mapping": "[{'topic': 'storm-events','db': '<enter database name>', 'table': 'Storms','format': 'csv', 'mapping':'Storms_CSV_Mapping'}]",
        "aad.auth.authority": "<enter tenant ID>",
        "aad.auth.appid": "<enter application ID>",
        "aad.auth.appkey": "<enter client secret>",
        "kusto.url": "https://ingest-<name of cluster>.<region>.kusto.windows.net",
        "key.converter": "org.apache.kafka.connect.storage.StringConverter",
        "value.converter": "org.apache.kafka.connect.storage.StringConverter"
    }
}
```

Azure 데이터 탐색기 설정에 따라 `aad.auth.authority` ,, `aad.auth.appid` `aad.auth.appkey` , `kusto.tables.topics.mapping` (데이터베이스 이름) 및 `kusto.url` 특성의 값을로 바꿉니다.

#### <a name="connector---dockerfile"></a>커넥터-Dockerfile

이 파일에는 커넥터 인스턴스의 docker 이미지를 생성 하는 명령이 있습니다.  Git 리포지토리 릴리스 디렉터리에서 커넥터 다운로드를 포함 합니다.

#### <a name="storm-events-producer-directory"></a>스톰-이벤트-생산자 디렉터리

이 디렉터리에는 로컬 "StormEvents.csv" 파일을 읽고 Kafka 토픽에 데이터를 게시 하는 Go 프로그램이 있습니다.

#### <a name="docker-composeyaml"></a>docker-작성 .yaml

```yaml
version: "2"
services:
  zookeeper:
    image: debezium/zookeeper:1.2
    ports:
      - 2181:2181
  kafka:
    image: debezium/kafka:1.2
    ports:
      - 9092:9092
    links:
      - zookeeper
    depends_on:
      - zookeeper
    environment:
      - ZOOKEEPER_CONNECT=zookeeper:2181
      - KAFKA_ADVERTISED_LISTENERS=PLAINTEXT://localhost:9092
  kusto-connect:
    build:
      context: ./connector
      args:
        KUSTO_KAFKA_SINK_VERSION: 1.0.1
    ports:
      - 8083:8083
    links:
      - kafka
    depends_on:
      - kafka
    environment:
      - BOOTSTRAP_SERVERS=kafka:9092
      - GROUP_ID=adx
      - CONFIG_STORAGE_TOPIC=my_connect_configs
      - OFFSET_STORAGE_TOPIC=my_connect_offsets
      - STATUS_STORAGE_TOPIC=my_connect_statuses
  events-producer:
    build:
      context: ./storm-events-producer
    links:
      - kafka
    depends_on:
      - kafka
    environment:
      - KAFKA_BOOTSTRAP_SERVER=kafka:9092
      - KAFKA_TOPIC=storm-events
      - SOURCE_FILE=StormEvents.csv
```

### <a name="start-the-containers"></a>컨테이너 시작

1. 터미널에서 컨테이너를 시작 합니다.
    
    ```shell
    docker-compose up
    ```

    생산자 응용 프로그램은 토픽으로 이벤트를 보내기 시작 합니다 `storm-events` . 
    다음 로그와 유사한 로그가 표시 됩니다.

    ```shell
    ....
    events-producer_1  | sent message to partition 0 offset 0
    events-producer_1  | event  2007-01-01 00:00:00.0000000,2007-01-01 00:00:00.0000000,13208,NORTH CAROLINA,Thunderstorm Wind,Public
    events-producer_1  | 
    events-producer_1  | sent message to partition 0 offset 1
    events-producer_1  | event  2007-01-01 00:00:00.0000000,2007-01-01 05:00:00.0000000,23358,WISCONSIN,Winter Storm,COOP Observer
    ....
    ```
    
1. 로그를 확인 하려면 별도의 터미널에서 다음 명령을 실행 합니다.

    ```shell
    docker-compose logs -f | grep kusto-connect
    ```
    
### <a name="start-the-connector"></a>커넥터 시작

커넥터를 시작 하려면 Kafka Connect REST 호출을 사용 합니다.

1. 별도의 터미널에서 다음 명령을 사용 하 여 싱크 작업을 시작 합니다.

    ```shell
    curl -X POST -H "Content-Type: application/json" --data @adx-sink-config.json http://localhost:8083/connectors
    ```
    
1. 상태를 확인 하려면 별도의 터미널에서 다음 명령을 실행 합니다.
    
    ```shell
    curl http://localhost:8083/connectors/storm/status
    ```

커넥터에서 Azure 데이터 탐색기에 대 한 수집 프로세스를 시작 합니다.

> [!NOTE]
> 로그 커넥터 문제가 있는 경우 문제를 [만듭니다](https://github.com/Azure/kafka-sink-azure-kusto/issues).

## <a name="query-and-review-data"></a>데이터 쿼리 및 검토

### <a name="confirm-data-ingestion"></a>데이터 수집 확인

1. 테이블에 데이터가 도착할 때까지 기다립니다 `Storms` . 데이터 전송을 확인 하려면 행 개수를 확인 합니다.
    
    ```kusto
    Storms | count
    ```

1. 수집 프로세스에 오류가 없는지 확인 합니다.

    ```kusto
    .show ingestion failures
    ```
    
    데이터가 표시 되 면 몇 가지 쿼리를 사용해 보세요. 

### <a name="query-the-data"></a>데이터 쿼리

1. 모든 레코드를 보려면 다음 [쿼리](write-queries.md)를 실행 합니다.
    
    ```kusto
    Storms
    ```

1. `where`및 `project` 를 사용 하 여 특정 데이터를 필터링 합니다.
    
    ```kusto
    Storms
    | where EventType == 'Drought' and State == 'TEXAS'
    | project StartTime, EndTime, Source, EventId
    ```
    
1. [`summarize`](./write-queries.md#summarize) 연산자를 사용합니다.

    ```kusto
    Storms
    | summarize event_count=count() by State
    | where event_count > 10
    | project State, event_count
    | render columnchart
    ```
    
    :::image type="content" source="media/ingest-data-kafka/kusto-query.png" alt-text="Azure 데이터 탐색기의 kafka 쿼리 세로 막대형 차트 결과":::

더 많은 쿼리 예제 및 지침은 [Azure 데이터 탐색기에 대 한 쿼리 작성](write-queries.md) 및 [kusto 쿼리 언어 설명서](./kusto/query/index.md)를 참조 하세요.

## <a name="reset"></a>다시 설정

다시 설정 하려면 다음 단계를 수행 합니다.

1. 컨테이너 중지 ( `docker-compose down -v` )
1. 삭제 (`drop table Storms`)
1. 테이블을 다시 만듭니다. `Storms`
1. 테이블 매핑 다시 만들기
1. 컨테이너 다시 시작 ( `docker-compose up` )

## <a name="clean-up-resources"></a>리소스 정리

Azure 데이터 탐색기 리소스를 삭제 하려면 [az cluster delete](/cli/azure/kusto/cluster#az-kusto-cluster-delete) 또는 [Az kusto database delete](/cli/azure/kusto/database#az-kusto-database-delete)를 사용 합니다.

```azurecli-interactive
az kusto cluster delete -n <cluster name> -g <resource group name>
az kusto database delete -n <database name> --cluster-name <cluster name> -g <resource group name>
```

## <a name="next-steps"></a>다음 단계

* [빅 데이터 아키텍처](/azure/architecture/solution-ideas/articles/big-data-azure-data-explorer)에 대해 자세히 알아보세요.
* [JSON 형식의 샘플 데이터를 Azure 데이터 탐색기에 수집 하는 방법](./ingest-json-formats.md?tabs=kusto-query-language)에 대해 알아봅니다.
* 추가 Kafka labs의 경우:
   * [분산 모드의 Confluent 클라우드 Kafka에서 수집 하기 위한 실습](https://github.com/Azure/azure-kusto-labs/blob/master/kafka-integration/confluent-cloud/README.md)
   * [분산 모드의 HDInsight Kafka에서 수집 하기 위한 실습](https://github.com/Azure/azure-kusto-labs/tree/master/kafka-integration/distributed-mode/hdinsight-kafka)
   * [분산 모드의 AKS Confluent IaaS Kafka에서 수집 하기 위한 실습](https://github.com/Azure/azure-kusto-labs/blob/master/kafka-integration/distributed-mode/confluent-kafka/README.md)
