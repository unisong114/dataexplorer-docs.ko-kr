---
title: Kafka에서 Azure Data Explorer로 데이터 수집
description: 이 문서에서는 Kafka에서 Azure 데이터 탐색기로 데이터를 수집(로드)하는 방법을 배웁니다.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 03b46ff50683149a22c71ccb155480a0f08455bd
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81500672"
---
# <a name="ingest-data-from-kafka-into-azure-data-explorer"></a>Kafka에서 Azure Data Explorer로 데이터 수집
 
Azure 데이터 탐색기는 로그 및 원격 분석 데이터에 사용 가능한 빠르고 확장성이 우수한 데이터 탐색 서비스입니다. Azure Data Explorer는 Kafka에서의 수집(데이터 로드)을 제공합니다. Kafka는 시스템 또는 애플리케이션 간에 데이터를 안정적으로 이동하는 실시간 스트리밍 데이터 파이프라인을 빌드할 수 있도록 하는 분산 스트리밍 플랫폼입니다.
 
## <a name="prerequisites"></a>사전 요구 사항
 
* Azure 구독이 아직 없는 경우 시작하기 전에 [Azure 체험 계정](https://azure.microsoft.com/free/)을 만듭니다. 
 
* [테스트 클러스터 및 데이터베이스](create-cluster-database-portal.md).
 
* 데이터를 생성하고 카프카로 전송하는 [샘플 앱입니다.](https://github.com/Azure/azure-kusto-samples-dotnet/tree/master/kafka)

* 샘플 앱을 실행하기 위한 [Visual Studio 2019](https://visualstudio.microsoft.com/vs/).
 
## <a name="kafka-connector-setup"></a>Kafka 커넥터 설치

Kafka Connect는 Apache Kafka와 기타 시스템 간에 측정 가능하면서 안정적으로 데이터를 스트리밍하기 위한 도구입니다. 이 도구를 사용하면 Kafka와 대규모 데이터 컬렉션을 주고 받는 커넥터를 신속하고 간단하게 정의할 수 있습니다. ADX Kafka Sink는 Kafka의 커넥터 역할을 합니다.
 
### <a name="bundle"></a>번들

Kafka는 `.jar`을 사용자 지정 커넥터로 작동할 플러그 인으로 로드할 수 있습니다. 이러한 `.jar`을 생성하기 위해 코드를 로컬로 복제하고 Maven을 사용하여 빌드합니다. 

#### <a name="clone"></a>복제

```bash
git clone git://github.com:Azure/kafka-sink-azure-kusto.git
cd ./kafka-sink-azure-kusto/kafka/
```

#### <a name="build"></a>빌드

Maven으로 로컬로 빌드하여 종속성을 갖춘 `.jar`을 생성합니다.

* JDK >= 1.8 [download](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* Maven [download](https://maven.apache.org/install.html)
 

루트 디렉터리 *kafka-sink-azure-kusto* 내부에서 다음을 실행합니다.

```bash
mvn clean compile assembly:single
```

### <a name="deploy"></a>배포 

Kafka에 플러그 인을 로드합니다. docker를 사용하는 배포 예제는 [kafka-sink-azure-kusto](https://github.com/Azure/kafka-sink-azure-kusto#deploy)에서 확인할 수 있습니다.
 

Kafka 커넥터와 이 커넥터를 배포하는 방법에 대한 자세한 설명서는 [Kafka Connect](https://kafka.apache.org/documentation/#connect)에서 찾을 수 있습니다. 

### <a name="example-configuration"></a>구성 예 
 
```config
name=KustoSinkConnector 
connector.class=com.microsoft.azure.kusto.kafka.connect.sink.KustoSinkConnector 
kusto.sink.flush_interval_ms=300000 
key.converter=org.apache.kafka.connect.storage.StringConverter 
value.converter=org.apache.kafka.connect.storage.StringConverter 
tasks.max=1 
topics=testing1 
kusto.tables.topics_mapping=[{'topic': 'testing1','db': 'daniel', 'table': 'TestTable','format': 'json', 'mapping':'TestMapping'}] 
kusto.auth.authority=XXX 
kusto.url=https://ingest-{mycluster}.kusto.windows.net/ 
kusto.auth.appid=XXX 
kusto.auth.appkey=XXX 
kusto.sink.tempdir=/var/tmp/ 
kusto.sink.flush_size=1000
```
 
## <a name="create-a-target-table-in-adx"></a>ADX에서 대상 테이블 만들기
 
Kafka가 데이터를 보낼 수 있는 ADX의 테이블을 만듭니다. **필수 구성 요소**에서 프로비전했던 클러스터와 데이터베이스에서 테이블을 만듭니다.
 
1. Azure Portal에서 클러스터로 이동한 후 **쿼리**를 선택합니다.
 
    ![쿼리 애플리케이션 링크](media/ingest-data-event-hub/query-explorer-link.png)
 
1. 다음 명령을 창에 복사하고 **실행**을 선택합니다.
 
    ```Kusto
    .create table TestTable (TimeStamp: datetime, Name: string, Metric: int, Source:string)
    ```
 
    ![쿼리 만들기 실행](media/ingest-data-event-hub/run-create-query.png)
 
1. 다음 명령을 창에 복사하고 **실행**을 선택합니다.
 
    ```Kusto
    .create table TestTable ingestion json mapping 'TestMapping' '[{"column":"TimeStamp","path":"$.timeStamp","datatype":"datetime"},{"column":"Name","path":"$.name","datatype":"string"},{"column":"Metric","path":"$.metric","datatype":"int"},{"column":"Source","path":"$.source","datatype":"string"}]'
    ```

    이 명령은 테이블(TestTable)의 열 이름과 데이터 형식에 들어오는 JSON 데이터를 매핑합니다.


## <a name="generate-sample-data"></a>샘플 데이터 생성

Kafka 클러스터가 ADX에 연결되었으므로 다운로드한 [샘플 앱](https://github.com/Azure-Samples/event-hubs-dotnet-ingest)을 사용하여 데이터를 생성합니다.

### <a name="clone"></a>복제

로컬로 샘플 앱을 복제합니다.

```cmd
git clone git://github.com:Azure/azure-kusto-samples-dotnet.git
cd ./azure-kusto-samples-dotnet/kafka/
```

### <a name="run-the-app"></a>앱 실행

1. Visual Studio에서 샘플 앱 솔루션을 엽니다.

1. `Program.cs` 파일에서 `connectionString` 상수를 Kafka 연결 문자열로 업데이트합니다.

    ```csharp    
    const string connectionString = @"<YourConnectionString>";
    ```

1. 앱을 빌드 및 실행합니다. 앱이 Kafka 클러스터로 메시지를 전송하고 10초마다 해당 상태를 출력합니다.

1. 앱이 몇 개의 메시지를 보내면 다음 단계로 이동합니다.
 
## <a name="query-and-review-the-data"></a>데이터 쿼리 및 검토

1. 수집하는 동안 오류가 발생하지 않도록 하려면:

    ```Kusto
    .show ingestion failures
    ```

1. 새로 수집된 데이터를 보려면

    ```Kusto
    TestTable 
    | count
    ```

1. 메시지의 콘텐츠를 보려면
 
    ```Kusto
    TestTable
    ```
 
    결과 세트는 다음과 같이 표시됩니다.
 
    ![메시지 결과 집합](media/ingest-data-event-hub/message-result-set.png)
 
## <a name="next-steps"></a>다음 단계
 
* [Azure 데이터 탐색기의 쿼리 데이터](web-query-data.md)
