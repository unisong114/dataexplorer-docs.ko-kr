---
title: 아파치 스파크에 대한 Azure 데이터 탐색기 커넥터를 사용하여 Azure 데이터 탐색기와 스파크 클러스터 간에 데이터를 이동합니다.
description: 이 항목에서는 Azure 데이터 탐색기와 아파치 스파크 클러스터 간에 데이터를 이동하는 방법을 보여 주며, 이 항목에서는 데이터를 이동하는 방법을 보여 주며, 이 에 대해 설명합니다.
author: orspod
ms.author: orspodek
ms.reviewer: michazag
ms.service: data-explorer
ms.topic: conceptual
ms.date: 1/14/2020
ms.openlocfilehash: 0c8d1add5f5f72ad96475df67ec2ff20bc9364c0
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81495524"
---
# <a name="azure-data-explorer-connector-for-apache-spark"></a>아파치 스파크용 Azure 데이터 탐색기 커넥터

[아파치 스파크는](https://spark.apache.org/) 대규모 데이터 처리를 위한 통합 분석 엔진입니다. Azure Data Explorer는 대용량 데이터에 대한 실시간 분석을 위한 빠르고 완벽하게 관리되는 데이터 분석 서비스입니다. 

Spark용 Azure 데이터 탐색기 커넥터는 모든 Spark 클러스터에서 실행할 수 있는 [오픈 소스 프로젝트입니다.](https://github.com/Azure/azure-kusto-spark) Azure 데이터 탐색기 및 Spark 클러스터에서 데이터를 이동하기 위한 데이터 원본 및 데이터 싱크를 구현합니다. Azure 데이터 탐색기 및 Apache Spark를 사용하여 데이터 기반 시나리오를 대상으로 빠르고 확장 가능한 응용 프로그램을 빌드할 수 있습니다. 예를 들어 기계 학습(ML), 추출-변환-로드(ETL) 및 로그 분석등이 있습니다. 커넥터를 사용하면 Azure Data Explorer는 쓰기, 읽기 및 쓰기스트림과 같은 표준 Spark 원본 및 싱크 작업에 대한 유효한 데이터 저장소가 됩니다.

일괄 처리 또는 스트리밍 모드에서 Azure 데이터 탐색기에 쓸 수 있습니다. Azure Data Explorer에서 읽기는 Azure Data Explorer의 데이터를 필터링하여 전송된 데이터의 양을 줄이는 열 프런치 및 조건자 푸시다운을 지원합니다.

이 항목에서는 Azure 데이터 탐색기 스파크 커넥터를 설치하고 구성하고 Azure 데이터 탐색기와 아파치 스파크 클러스터 간에 데이터를 이동하는 방법에 대해 설명합니다.

> [!NOTE]
> 아래 예제 중 일부는 Azure [Databricks](https://docs.azuredatabricks.net/) 스파크 클러스터를 참조하지만 Azure Data Explorer Spark 커넥터는 Databricks 또는 기타 스파크 배포에 대한 직접 적인 종속성을 사용하지 않습니다.

## <a name="prerequisites"></a>사전 요구 사항

* [Azure 데이터 탐색기 클러스터 및 데이터베이스 만들기](/azure/data-explorer/create-cluster-database-portal) 
* Spark 클러스터 만들기
* Azure 데이터 탐색기 커넥터 라이브러리 설치:
    * [스파크 2.4, 스칼라 2.11을](https://github.com/Azure/azure-kusto-spark/releases) 위한 사전 제작 된 라이브러리 
    * [메이븐 리포지토리](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/spark-kusto-connector)
* [메이븐 3.x](https://maven.apache.org/download.cgi) 설치

> [!TIP]
> 2.3.x 버전도 지원되지만 pom.xml 종속성에 약간의 변경이 필요할 수 있습니다.

## <a name="how-to-build-the-spark-connector"></a>스파크 커넥터 를 구축하는 방법

> [!NOTE]
> 이 단계는 선택 사항입니다. 미리 빌드된 라이브러리를 사용하는 경우 [Spark 클러스터 설정으로](#spark-cluster-setup)이동합니다.

### <a name="build-prerequisites"></a>필수 구성 조건 빌드

1. 다음 [Kusto Java SDK](kusto/api/java/kusto-java-client-library.md) 라이브러리를 포함하여 [종속성에](https://github.com/Azure/azure-kusto-spark#dependencies) 나열된 라이브러리를 설치합니다.
    * [쿠스토 데이터 클라이언트](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/kusto-data)
    * [쿠스토 인제스트 클라이언트](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/kusto-ingest)

1. 스파크 커넥터를 작성하려면 [이 소스를](https://github.com/Azure/azure-kusto-spark) 참조하십시오.

1. Maven 프로젝트 정의를 사용하는 스칼라/Java 응용 프로그램의 경우 응용 프로그램을 다음 아티팩트와 연결합니다(최신 버전은 다를 수 있음).
    
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

Jar를 빌드하려면 모든 테스트를 실행하고 해당 지역의 Maven 저장소에 jar을 설치합니다.

```
mvn clean install
```

자세한 내용은 [커넥터 사용을](https://github.com/Azure/azure-kusto-spark#usage)참조하십시오.

## <a name="spark-cluster-setup"></a>스파크 클러스터 설정

> [!NOTE]
> 다음 단계를 수행할 때 최신 Azure 데이터 탐색기 Spark 커넥터 릴리스를 사용하는 것이 좋습니다.

1. Spark 2.4.4 및 Scala 2.11을 사용하여 Azure Databricks 클러스터를 기반으로 다음 스파크 클러스터 설정을 구성합니다.

    ![데이터 브릭 클러스터 설정](media/spark-connector/databricks-cluster.png)
    
1. Maven의 최신 스파크 쿠스토 커넥터 라이브러리 설치:
    
    ![가져오기](media/spark-connector/db-libraries-view.png) ![라이브러리 선택 스파크-쿠스토 커넥터](media/spark-connector/db-dependencies.png)

1. 필요한 모든 라이브러리가 설치되었는지 확인합니다.

    ![라이브러리 설치 확인](media/spark-connector/db-libraries-view.png)

1. JAR 파일을 사용하여 설치하는 경우 추가 종속성이 설치되었는지 확인합니다.

    ![종속성 추가](media/spark-connector/db-not-maven.png)

## <a name="authentication"></a>인증

Azure 데이터 탐색기 스파크 커넥터를 사용하면 다음 방법 중 하나를 사용하여 Azure Active Directory(Azure AD)로 인증할 수 있습니다.
* [Azure AD 응용 프로그램](#azure-ad-application-authentication)
* [Azure AD 액세스 토큰](https://github.com/Azure/azure-kusto-spark/blob/dev/docs/Authentication.md#direct-authentication-with-access-token)
* [장치](https://github.com/Azure/azure-kusto-spark/blob/dev/docs/Authentication.md#device-authentication) 인증(비프로덕션 시나리오용)
* [Azure 키 볼트](https://github.com/Azure/azure-kusto-spark/blob/dev/docs/Authentication.md#key-vault) 키 볼트 키 볼트 리소스에 액세스하고 Azure 키볼트 패키지를 설치하고 응용 프로그램 자격 증명을 제공합니다.

### <a name="azure-ad-application-authentication"></a>Azure AD 응용 프로그램 인증

Azure AD 응용 프로그램 인증은 가장 간단하고 일반적인 인증 방법이며 Azure 데이터 탐색기 스파크 커넥터에 권장됩니다.

|속성  |Description  |
|---------|---------|
|**KUSTO_AAD_CLIENT_ID**     |   Azure AD 응용 프로그램(클라이언트) 식별자입니다.      |
|**KUSTO_AAD_AUTHORITY_ID**     |  Azure AD 인증 기관입니다. Azure AD 디렉터리(테넌트) ID입니다.        |
|**KUSTO_AAD_CLIENT_PASSWORD**    |    클라이언트에 대한 Azure AD 응용 프로그램 키입니다.     |

### <a name="azure-data-explorer-privileges"></a>Azure 데이터 탐색기 권한

Azure 데이터 탐색기 클러스터에 다음 권한을 부여합니다.

* 읽기(데이터 원본)의 경우 Azure AD ID에는 대상 데이터베이스에 대한 *뷰어* 권한이 있거나 대상 테이블에 *대한 관리자* 권한이 있어야 합니다.
* 쓰기(데이터 싱크)의 경우 Azure AD ID에는 대상 데이터베이스에 *인제스터* 권한이 있어야 합니다. 또한 새 테이블을 만들려면 대상 데이터베이스에 *사용자* 권한이 있어야 합니다. 대상 테이블이 이미 있는 경우 대상 테이블에서 *관리자* 권한을 구성해야 합니다.
 
Azure Data Explorer 주 가지 주 역할에 대한 자세한 내용은 [역할 기반 권한 부여](kusto/management/access-control/role-based-authorization.md)를 참조하십시오. 보안 역할을 관리하기 위해 [보안 역할 관리를](kusto/management/security-roles.md)참조하십시오.

## <a name="spark-sink-writing-to-azure-data-explorer"></a>스파크 싱크: Azure 데이터 탐색기로 쓰기

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

1. Azure 데이터 탐색기 클러스터에 스파크 DataFrame을 일괄 처리로 작성합니다.

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
    
   또는 단순화된 구문을 사용합니다.
   
    ```scala
         import com.microsoft.kusto.spark.datasink.SparkIngestionProperties
         import com.microsoft.kusto.spark.sql.extension.SparkExtension._
         
         val sparkIngestionProperties = Some(new SparkIngestionProperties()) // Optional, use None if not needed
         df.write.kusto(cluster, database, table, conf, sparkIngestionProperties)
    ```
   
1. 스트리밍 데이터 작성:

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

## <a name="spark-source-reading-from-azure-data-explorer"></a>스파크 소스: Azure 데이터 탐색기에서 읽기

1. [소량의 데이터를](kusto/concepts/querylimits.md)읽을 때 데이터 쿼리를 정의합니다.

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

1. 선택 사항: Azure 데이터 탐색기가 아닌 일시적인 Blob 저장소를 제공하는 **경우** Blob이 생성되면 호출자의 책임이 있습니다. 여기에는 저장소 프로비저닝, 액세스 키 회전 및 일시적인 아티팩트 삭제가 포함됩니다. 
    KustoBlobStorageUtils 모듈에는 계정 및 컨테이너 좌표 및 계정 자격 증명을 기반으로 Blob을 삭제하는 도우미 기능 또는 쓰기, 읽기 및 목록 권한이 있는 전체 SAS URL이 포함되어 있습니다. 해당 RDD가 더 이상 필요하지 않은 경우 각 트랜잭션은 일시적인 Blob 아티팩트를 별도의 디렉터리에 저장합니다. 이 디렉터리는 Spark Driver 노드에 보고된 읽기 트랜잭션 정보 로그의 일부로 캡처됩니다.

    ```scala
    // Use either container/account-key/account name, or container SaS
    val container = dbutils.secrets.get(scope = "KustoDemos", key = "blobContainer")
    val storageAccountKey = dbutils.secrets.get(scope = "KustoDemos", key = "blobStorageAccountKey")
    val storageAccountName = dbutils.secrets.get(scope = "KustoDemos", key = "blobStorageAccountName")
    // val storageSas = dbutils.secrets.get(scope = "KustoDemos", key = "blobStorageSasUrl")
    ```

    위의 예에서 키 볼트는 커넥터 인터페이스를 사용하여 액세스되지 않습니다. Databricks 비밀을 사용하는 더 간단한 방법이 사용됩니다.

1. Azure 데이터 탐색기에서 읽기

    * 일시적인 Blob 저장소를 제공하는 **경우** Azure 데이터 탐색기에서 다음과 같이 읽어보십시오.

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

    * **Azure Data 탐색기가** 일시적인 Blob 저장소를 제공하는 경우 Azure 데이터 탐색기에서 다음과 같이 읽어보십시오.
    
        ```scala
        val dfFiltered = df2
          .where(df2.col("ColA").startsWith("row-2"))
          .filter("ColB > 12")
          .filter("ColB <= 21")
          .select("ColA")
        
        display(dfFiltered)
        ```

## <a name="next-steps"></a>다음 단계

* Azure 데이터 [탐색기 스파크 커넥터에](https://github.com/Azure/azure-kusto-spark/tree/master/docs) 대해 자세히 알아보기
* [자바와 파이썬에 대한 샘플 코드](https://github.com/Azure/azure-kusto-spark/tree/master/samples/src/main)
