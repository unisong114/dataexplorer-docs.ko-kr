---
title: Apache Spark Azure 데이터 탐색기 커넥터를 사용 하 여 Azure 데이터 탐색기와 Spark 클러스터 간에 데이터를 이동 합니다.
description: 이 항목에서는 Azure 데이터 탐색기와 Apache Spark 클러스터 간에 데이터를 이동 하는 방법을 보여 줍니다.
author: orspod
ms.author: orspodek
ms.reviewer: michazag
ms.service: data-explorer
ms.topic: conceptual
ms.date: 1/14/2020
ms.openlocfilehash: 28dee67b6ac412a9c0497d5713a69c9617d3ae55
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/13/2020
ms.locfileid: "83370459"
---
# <a name="azure-data-explorer-connector-for-apache-spark"></a>Apache Spark 용 Azure 데이터 탐색기 커넥터

[Apache Spark](https://spark.apache.org/) 는 대규모 데이터 처리를 위한 통합 분석 엔진입니다. Azure 데이터 탐색기는 대량의 데이터를 실시간으로 분석할 수 있는 빠르고 완전히 관리 되는 데이터 분석 서비스입니다. 

Spark에 대 한 Azure 데이터 탐색기 커넥터는 모든 Spark 클러스터에서 실행할 수 있는 [오픈 소스 프로젝트](https://github.com/Azure/azure-kusto-spark) 입니다. Azure 데이터 탐색기 및 Spark 클러스터에서 데이터를 이동 하기 위한 데이터 원본 및 데이터 싱크를 구현 합니다. Azure 데이터 탐색기 및 Apache Spark를 사용 하 여 데이터 기반 시나리오를 대상으로 하는 빠르고 확장 가능한 응용 프로그램을 빌드할 수 있습니다. 예를 들어 기계 학습 (ML), ETL (추출-변환-로드) 및 Log Analytics입니다. 커넥터를 사용 하는 경우 Azure 데이터 탐색기은 쓰기, 읽기, writeStream 등의 표준 Spark 원본 및 싱크 작업에 유효한 데이터 저장소가 됩니다.

일괄 처리 또는 스트리밍 모드에서 Azure 데이터 탐색기에 쓸 수 있습니다. Azure 데이터 탐색기에서 읽으면 Azure 데이터 탐색기의 데이터를 필터링 하 여 전송 되는 데이터의 양을 줄이는 열 정리 및 조건자 푸시 다운이 지원 됩니다.

이 항목에서는 azure 데이터 탐색기 Spark 커넥터를 설치 및 구성 하 고 Azure 데이터 탐색기와 Apache Spark 클러스터 간에 데이터를 이동 하는 방법을 설명 합니다.

> [!NOTE]
> 아래 예제 중 일부는 [Azure Databricks](https://docs.azuredatabricks.net/) spark 클러스터를 참조 하지만, Azure 데이터 탐색기 spark 커넥터는 Databricks 또는 다른 spark 배포에 대 한 직접적인 종속성을 취하지 않습니다.

## <a name="prerequisites"></a>필수 구성 요소

* [Azure 데이터 탐색기 클러스터 및 데이터베이스 만들기](create-cluster-database-portal.md) 
* Spark 클러스터 만들기
* Azure 데이터 탐색기 커넥터 라이브러리를 설치 합니다.
    * Spark 2.4에 대 한 미리 빌드된 라이브러리 [, Scala 2.11](https://github.com/Azure/azure-kusto-spark/releases) 
    * [Maven 리포지토리](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/spark-kusto-connector)
* [Maven](https://maven.apache.org/download.cgi) 3.x 설치 됨

> [!TIP]
> 2.3. x 버전도 지원 되지만 pom .xml 종속성의 일부를 변경 해야 할 수도 있습니다.

## <a name="how-to-build-the-spark-connector"></a>Spark 커넥터를 빌드하는 방법

> [!NOTE]
> 이 단계는 선택 사항입니다. 미리 빌드된 라이브러리를 사용 하는 경우 [Spark 클러스터 설정](#spark-cluster-setup)으로 이동 합니다.

### <a name="build-prerequisites"></a>빌드 필수 구성 요소

1. 다음 [Kusto JAVA SDK](kusto/api/java/kusto-java-client-library.md) 라이브러리를 포함 하 여 [종속성](https://github.com/Azure/azure-kusto-spark#dependencies) 에 나열 된 라이브러리를 설치 합니다.
    * [Kusto 데이터 클라이언트](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/kusto-data)
    * [Kusto 수집 클라이언트](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/kusto-ingest)

1. Spark 커넥터를 빌드하려면 [이 소스](https://github.com/Azure/azure-kusto-spark) 를 참조 하세요.

1. Maven 프로젝트 정의를 사용 하는 Scala/Java 응용 프로그램의 경우 다음 아티팩트를 사용 하 여 응용 프로그램을 연결 합니다 (최신 버전은 다를 수 있음).
    
    ```Maven
       <dependency>
         <groupId>com.microsoft.azure</groupId>
         <artifactId>spark-kusto-connector</artifactId>
         <version>1.1.0</version>
       </dependency>
    ```

### <a name="build-commands"></a>빌드 명령

jar를 빌드하고 모든 테스트를 실행하려면:

```
mvn clean package
```

Jar을 빌드하려면 모든 테스트를 실행 하 고 로컬 Maven 리포지토리에 jar을 설치 합니다.

```
mvn clean install
```

자세한 내용은 [커넥터 사용](https://github.com/Azure/azure-kusto-spark#usage)을 참조 하세요.

## <a name="spark-cluster-setup"></a>Spark 클러스터 설정

> [!NOTE]
> 다음 단계를 수행 하는 경우 최신 Azure 데이터 탐색기 Spark 커넥터 릴리스를 사용 하는 것이 좋습니다.

1. Spark 2.4.4 및 Scala 2.11를 사용 하 여 Azure Databricks 클러스터에 따라 다음 Spark 클러스터 설정을 구성 합니다.

    ![Databricks 클러스터 설정](media/spark-connector/databricks-cluster.png)
    
1. Maven에서 최신 spark-kusto 커넥터 라이브러리를 설치 합니다.
    
    ![라이브러리 가져오기 ](media/spark-connector/db-libraries-view.png) ![ Spark-Kusto-커넥터를 선택 합니다.](media/spark-connector/db-dependencies.png)

1. 모든 필수 라이브러리가 설치 되어 있는지 확인 합니다.

    ![설치 된 라이브러리 확인](media/spark-connector/db-libraries-view.png)

1. JAR 파일을 사용 하 여 설치 하려면 추가 종속성이 설치 되어 있는지 확인 합니다.

    ![종속성 추가](media/spark-connector/db-not-maven.png)

## <a name="authentication"></a>인증

Azure 데이터 탐색기 Spark 커넥터를 사용 하면 다음 방법 중 하나를 사용 하 여 Azure Active Directory (Azure AD)에 인증할 수 있습니다.
* [AZURE AD 응용 프로그램](#azure-ad-application-authentication)
* [AZURE AD 액세스 토큰](https://github.com/Azure/azure-kusto-spark/blob/master/docs/Authentication.md#direct-authentication-with-access-token)
* [장치 인증](https://github.com/Azure/azure-kusto-spark/blob/master/docs/Authentication.md#device-authentication) (비프로덕션 시나리오의 경우)
* [Azure Key Vault](https://github.com/Azure/azure-kusto-spark/blob/master/docs/Authentication.md#key-vault) Key Vault 리소스에 액세스 하 고, Azure-keyvault 패키지를 설치 하 고, 응용 프로그램 자격 증명을 제공 합니다.

### <a name="azure-ad-application-authentication"></a>Azure AD 응용 프로그램 인증

Azure AD 응용 프로그램 인증은 가장 간단 하 고 가장 일반적인 인증 방법 이며 Azure 데이터 탐색기 Spark 커넥터에 권장 됩니다.

|속성  |설명  |
|---------|---------|
|**KUSTO_AAD_CLIENT_ID**     |   Azure AD 응용 프로그램 (클라이언트) 식별자입니다.      |
|**KUSTO_AAD_AUTHORITY_ID**     |  Azure AD 인증 기관. Azure AD 디렉터리 (테 넌 트) ID입니다.        |
|**KUSTO_AAD_CLIENT_PASSWORD**    |    클라이언트의 Azure AD 응용 프로그램 키입니다.     |

### <a name="azure-data-explorer-privileges"></a>Azure 데이터 탐색기 권한

Azure 데이터 탐색기 클러스터에 대 한 다음 권한을 부여 합니다.

* 읽기 (데이터 원본)의 경우 Azure AD id는 대상 데이터베이스에 대 한 *뷰어* 권한 또는 대상 테이블에 대 한 *관리자* 권한이 있어야 합니다.
* 쓰기 (데이터 싱크)의 경우 Azure AD id에는 대상 데이터베이스에 대 한 *수집기* 권한이 있어야 합니다. 또한 새 테이블을 만들려면 대상 데이터베이스에 대 한 *사용자* 권한이 있어야 합니다. 대상 테이블이 이미 있는 경우 대상 테이블에 대해 *관리자* 권한을 구성 해야 합니다.
 
Azure 데이터 탐색기 주 역할에 대 한 자세한 내용은 [역할 기반 권한 부여](kusto/management/access-control/role-based-authorization.md)를 참조 하세요. 보안 역할 관리에 대 한는 [보안 역할 관리](kusto/management/security-roles.md)를 참조 하세요.

## <a name="spark-sink-writing-to-azure-data-explorer"></a>Spark 싱크: Azure 데이터 탐색기에 쓰기

1. 싱크 매개 변수 설정:

     ```scala
    val KustoSparkTestAppId = dbutils.secrets.get(scope = "KustoDemos", key = "KustoSparkTestAppId")
    val KustoSparkTestAppKey = dbutils.secrets.get(scope = "KustoDemos", key = "KustoSparkTestAppKey")
 
    val appId = KustoSparkTestAppId
    val appKey = KustoSparkTestAppKey
    val authorityId = "72f988bf-86f1-41af-91ab-2d7cd011db47" // Optional - defaults to microsoft.com
    val cluster = "Sparktest.eastus2"
    val database = "TestDb"
    val table = "StringAndIntTable"
    ```

1. Azure 데이터 탐색기 클러스터에 batch로 Spark 데이터 프레임을 씁니다.

    ```scala
    import com.microsoft.kusto.spark.datasink.KustoSinkOptions
    import org.apache.spark.sql.{SaveMode, SparkSession}

    df.write
      .format("com.microsoft.kusto.spark.datasource")
      .option(KustoSinkOptions.KUSTO_CLUSTER, cluster)
      .option(KustoSinkOptions.KUSTO_DATABASE, database)
      .option(KustoSinkOptions.KUSTO_TABLE, "Demo3_spark")
      .option(KustoSinkOptions.KUSTO_AAD_CLIENT_ID, appId)
      .option(KustoSinkOptions.KUSTO_AAD_CLIENT_PASSWORD, appKey)
      .option(KustoSinkOptions.KUSTO_AAD_AUTHORITY_ID, authorityId)
      .option(KustoSinkOptions.KUSTO_TABLE_CREATE_OPTIONS, "CreateIfNotExist")
      .mode(SaveMode.Append)
      .save()  
    ```
    
   또는 간단한 구문을 사용 합니다.
   
    ```scala
         import com.microsoft.kusto.spark.datasink.SparkIngestionProperties
         import com.microsoft.kusto.spark.sql.extension.SparkExtension._
         
         val sparkIngestionProperties = Some(new SparkIngestionProperties()) // Optional, use None if not needed
         df.write.kusto(cluster, database, table, conf, sparkIngestionProperties)
    ```
   
1. 스트리밍 데이터를 씁니다.

    ```scala    
    import org.apache.spark.sql.streaming.Trigger
    import java.util.concurrent.TimeUnit
    import java.util.concurrent.TimeUnit
    import org.apache.spark.sql.streaming.Trigger

    // Set up a checkpoint and disable codeGen. 
    spark.conf.set("spark.sql.streaming.checkpointLocation", "/FileStore/temp/checkpoint")
        
    // Write to a Kusto table from a streaming source
    val kustoQ = df
          .writeStream
          .format("com.microsoft.kusto.spark.datasink.KustoSinkProvider")
          .options(conf) 
          .option(KustoSinkOptions.KUSTO_WRITE_ENABLE_ASYNC, "true") // Optional, better for streaming, harder to handle errors
          .trigger(Trigger.ProcessingTime(TimeUnit.SECONDS.toMillis(10))) // Sync this with the ingestionBatching policy of the database
          .start()
    ```

## <a name="spark-source-reading-from-azure-data-explorer"></a>Spark 원본: Azure 데이터 탐색기에서 읽기

1. [소량의 데이터](kusto/concepts/querylimits.md)를 읽을 때 데이터 쿼리를 정의 합니다.

    ```scala
    import com.microsoft.kusto.spark.datasource.KustoSourceOptions
    import org.apache.spark.SparkConf
    import org.apache.spark.sql._
    import com.microsoft.azure.kusto.data.ClientRequestProperties

    val query = s"$table | where (ColB % 1000 == 0) | distinct ColA"
    val conf: Map[String, String] = Map(
          KustoSourceOptions.KUSTO_AAD_CLIENT_ID -> appId,
          KustoSourceOptions.KUSTO_AAD_CLIENT_PASSWORD -> appKey
        )

    val df = spark.read.format("com.microsoft.kusto.spark.datasource").
      options(conf).
      option(KustoSourceOptions.KUSTO_QUERY, query).
      option(KustoSourceOptions.KUSTO_DATABASE, database).
      option(KustoSourceOptions.KUSTO_CLUSTER, cluster).
      load()

    // Simplified syntax flavor
    import com.microsoft.kusto.spark.sql.extension.SparkExtension._
    
    val cpr: Option[ClientRequestProperties] = None // Optional
    val df2 = spark.read.kusto(cluster, database, query, conf, cpr)
    display(df2)
    ```

1. 선택 사항: 임시 blob 저장소 (Azure 데이터 탐색기가 아닌)를 제공 **하** 는 경우 생성 되는 blob는 호출자의 책임입니다. 여기에는 저장소 프로 비전, 액세스 키 회전 및 임시 아티팩트 삭제가 포함 됩니다. 
    KustoBlobStorageUtils 모듈에는 계정 및 컨테이너 좌표와 계정 자격 증명을 기반으로 blob을 삭제 하는 도우미 함수 또는 쓰기, 읽기 및 나열 권한이 있는 전체 SAS URL이 포함 되어 있습니다. 해당 RDD가 더 이상 필요 하지 않은 경우 각 트랜잭션은 임시 blob 아티팩트를 별도의 디렉터리에 저장 합니다. 이 디렉터리는 Spark 드라이버 노드에 보고 된 읽기-트랜잭션 정보 로그의 일부로 캡처됩니다.

    ```scala
    // Use either container/account-key/account name, or container SaS
    val container = dbutils.secrets.get(scope = "KustoDemos", key = "blobContainer")
    val storageAccountKey = dbutils.secrets.get(scope = "KustoDemos", key = "blobStorageAccountKey")
    val storageAccountName = dbutils.secrets.get(scope = "KustoDemos", key = "blobStorageAccountName")
    // val storageSas = dbutils.secrets.get(scope = "KustoDemos", key = "blobStorageSasUrl")
    ```

    위의 예제에서 Key Vault 커넥터 인터페이스를 사용 하 여 액세스 되지 않습니다. Databricks 암호를 사용 하는 간단한 방법이 사용 됩니다.

1. Azure 데이터 탐색기에서 읽습니다.

    * 임시 blob storage를 제공 **하** 는 경우 다음과 같이 Azure 데이터 탐색기에서 읽습니다.

        ```scala
         val conf3 = Map(
              KustoSourceOptions.KUSTO_AAD_CLIENT_ID -> appId,
              KustoSourceOptions.KUSTO_AAD_CLIENT_PASSWORD -> appKey
              KustoSourceOptions.KUSTO_BLOB_STORAGE_SAS_URL -> storageSas)
        val df2 = spark.read.kusto(cluster, database, "ReallyBigTable", conf3)
        
        val dfFiltered = df2
          .where(df2.col("ColA").startsWith("row-2"))
          .filter("ColB > 12")
          .filter("ColB <= 21")
          .select("ColA")
        
        display(dfFiltered)
        ```

    * **Azure 데이터 탐색기** 에서 임시 blob storage를 제공 하는 경우 다음과 같이 azure 데이터 탐색기에서 읽습니다.
    
        ```scala
        val dfFiltered = df2
          .where(df2.col("ColA").startsWith("row-2"))
          .filter("ColB > 12")
          .filter("ColB <= 21")
          .select("ColA")
        
        display(dfFiltered)
        ```

## <a name="next-steps"></a>다음 단계

* [Azure 데이터 탐색기 Spark 커넥터](https://github.com/Azure/azure-kusto-spark/tree/master/docs) 에 대 한 자세한 정보
* [Java 및 Python에 대 한 샘플 코드](https://github.com/Azure/azure-kusto-spark/tree/master/samples/src/main)
