---
title: Kusto. 클라이언트 인터페이스 및 팩터리 클래스 수집-Azure 데이터 탐색기
description: 이 문서에서는 Kusto. Azure 데이터 탐색기에서 클라이언트 인터페이스 및 팩터리 클래스를 수집 하는 방법을 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: 1d3c3939a5c8b3a5f1e6f1fa0b40f9b927ee5325
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83226060"
---
# <a name="kustoingest-client-interfaces-and-factory-classes"></a>Kusto. 클라이언트 인터페이스 및 팩터리 클래스 수집

Kusto. 수집 라이브러리의 기본 인터페이스 및 팩터리 클래스는 다음과 같습니다.

* [인터페이스 IKustoIngestClient](#interface-ikustoingestclient): 기본 수집 인터페이스입니다.
* [클래스 ExtendedKustoIngestClient](#class-extendedkustoingestclient): 주 수집 인터페이스에 대 한 확장입니다.
* [클래스 KustoIngestFactory](#class-kustoingestfactory): 수집 클라이언트에 대 한 기본 팩터리입니다.
* [클래스 KustoIngestionProperties](#class-kustoingestionproperties): 일반적인 수집 속성을 제공 하는 데 사용 되는 클래스입니다.
* [클래스 JsonColumnMapping](#class-jsoncolumnmapping): JSON 데이터 원본에서 수집 때 적용할 스키마 매핑을 설명 하는 데 사용 되는 클래스입니다.
* [클래스 CsvColumnMapping](#class-csvcolumnmapping): CSV 데이터 원본에서 수집 때 적용할 스키마 매핑을 설명 하는 데 사용 되는 클래스입니다.
* [DataSourceFormat 열거형](#enum-datasourceformat): 지원 되는 데이터 원본 형식 (예: CSV, JSON)
* [인터페이스 IKustoQueuedIngestClient](#interface-ikustoqueuedingestclient): 대기 중인 수집에만 적용 되는 작업을 설명 하는 인터페이스입니다.
* [클래스 KustoQueuedIngestionProperties](#class-kustoqueuedingestionproperties): 대기 중인 수집에만 적용 되는 속성입니다.

## <a name="interface-ikustoingestclient"></a>인터페이스 IKustoIngestClient

* IngestFromDataReaderAsync
* IngestFromStorageAsync
* IngestFromStreamAsync

```csharp
public interface IKustoIngestClient : IDisposable
{
    /// <summary>
    /// Ingests data from <see cref="IDataReader"/>. <paramref name="dataReader"/> will be closed when the call completes.
    /// </summary>
    /// <param name="dataReader">The <see cref="IDataReader"/> data source to ingest. Only the first record set will be used</param>
    /// <param name="ingestionProperties">Additional properties to be used during the ingestion process</param>
    /// <param name="sourceOptions">Options for the <see cref="IDataReader"/> ingestion source. This is an optional parameter</param>
    /// <returns>An <see cref="IKustoIngestionResult"/> task</returns>
    Task<IKustoIngestionResult> IngestFromDataReaderAsync(IDataReader dataReader, KustoIngestionProperties ingestionProperties, DataReaderSourceOptions sourceOptions = null);

    /// <summary>
    /// Ingest data from one of the supported storage providers. Currently the supported providers are: File System, Azure Blob Storage.
    /// </summary>
    /// <param name="uri">The URI of the storage resource to be ingested. Note: This URI may include a storage account key or shared access signature (SAS).
    ///  See <see href="https://docs.microsoft.com/azure/kusto/api/connection-strings/storage"/> for the URI format options.</param>
    /// <param name="ingestionProperties">Additional properties to be used during the ingestion process</param>
    /// <param name="sourceOptions">Options for the storage ingestion source. This is an optional parameter</param>
    /// <returns>An <see cref="IKustoIngestionResult"/> task</returns>
    Task<IKustoIngestionResult> IngestFromStorageAsync(string uri, KustoIngestionProperties ingestionProperties, StorageSourceOptions sourceOptions = null);

    /// <summary>
    /// Ingest data from <see cref="Stream"/>.
    /// </summary>
    /// <param name="stream">The <see cref="Stream"/> data source to ingest</param>
    /// <param name="ingestionProperties">Additional properties to be used during the ingestion process</param>
    /// <param name="sourceOptions">Options for the <see cref="Stream"/> ingestion source. This is an optional parameter</param>
    /// <returns>An <see cref="IKustoIngestionResult"/> task</returns>
    Task<IKustoIngestionResult> IngestFromStreamAsync(Stream stream, KustoIngestionProperties ingestionProperties, StreamSourceOptions sourceOptions = null);
}
```

## <a name="class-extendedkustoingestclient"></a>클래스 ExtendedKustoIngestClient

* IngestFromSingleBlob-사용 되지 않습니다. 대신 `IKustoIngestClient.IngestFromStorageAsync`를 사용하세요.
* IngestFromSingleBlobAsync-사용 되지 않습니다. 대신 `IKustoIngestClient.IngestFromStorageAsync`를 사용하세요.
* IngestFromDataReader-사용 되지 않습니다. 대신 `IKustoIngestClient.IngestFromDataReaderAsync`를 사용하세요.
* IngestFromDataReaderAsync
* IngestFromSingleFile-사용 되지 않습니다. 대신 `IKustoIngestClient.IngestFromStorageAsync`를 사용하세요.
* IngestFromSingleFileAsync-사용 되지 않습니다. 대신 `IKustoIngestClient.IngestFromStorageAsync`를 사용하세요.
* IngestFromStream-사용 되지 않습니다. 대신 `IKustoIngestClient.IngestFromStreamAsync`를 사용하세요.
* IngestFromStreamAsync

```csharp
public static class ExtendedKustoIngestClient
{
    /// <summary>
    /// Ingest data from a single data blob
    /// </summary>
    /// <param name="client">The ingest client that will execute the ingestions</param>
    /// <param name="blobUri">The URI of the blob will be ingested</param>
    /// <param name="deleteSourceOnSuccess">Indicates if the source blob should be deleted after a successful ingestion</param>
    /// <param name="ingestionProperties">Additional properties to be used during the ingestion process</param>
    /// <param name="rawDataSize">The uncompressed raw data size</param>
    /// <returns><see cref="IKustoIngestionResult"/></returns>
    public static IKustoIngestionResult IngestFromSingleBlob(this IKustoIngestClient client, string blobUri, bool deleteSourceOnSuccess, KustoIngestionProperties ingestionProperties, long? rawDataSize = null);

    /// <summary>
    /// Ingest data from a single data blob asynchronously
    /// </summary>
    /// <param name="client">The ingest client that will execute the ingestions</param>
    /// <param name="blobUri">The URI of the blob will be ingested</param>
    /// <param name="deleteSourceOnSuccess">Indicates if the source blob should be deleted after a successful ingestion</param>
    /// <param name="ingestionProperties">Additional properties to be used during the ingestion process</param>
    /// <param name="rawDataSize">The uncompressed raw data size</param>
    /// <returns>An <see cref="IKustoIngestionResult"/> task</returns>
    public static Task<IKustoIngestionResult> IngestFromSingleBlobAsync(this IKustoIngestClient client, string blobUri, bool deleteSourceOnSuccess, KustoIngestionProperties ingestionProperties, long? rawDataSize = null);

    /// <summary>
    /// Ingest data from a single data blob
    /// </summary>
    /// <param name="client">The ingest client that will execute the ingestions</param>
    /// <param name="blobDescription"><see cref="BlobDescription"/> representing the blobs that will be ingested</param>
    /// <param name="deleteSourceOnSuccess">Indicates if the source blob should be deleted after a successful ingestion</param>
    /// <param name="ingestionProperties">Additional properties to be used during the ingestion process</param>
    /// <param name="rawDataSize">The uncompressed raw data size</param>
    /// <returns><see cref="IKustoIngestionResult"/></returns>
    public static IKustoIngestionResult IngestFromSingleBlob(this IKustoIngestClient client, BlobDescription blobDescription, bool deleteSourceOnSuccess, KustoIngestionProperties ingestionProperties, long? rawDataSize = null);

    /// <summary>
    /// Ingest data from a single data blob asynchronously
    /// </summary>
    /// <param name="client">The ingest client that will execute the ingestions</param>
    /// <param name="blobDescription"><see cref="BlobDescription"/> representing the blobs that will be ingested</param>
    /// <param name="deleteSourceOnSuccess">Indicates if the source blob should be deleted after a successful ingestion</param>
    /// <param name="ingestionProperties">Additional properties to be used during the ingestion process</param>
    /// <param name="rawDataSize">The uncompressed raw data size</param>
    /// <returns>An <see cref="IKustoIngestionResult"/> task</returns>
    public static Task<IKustoIngestionResult> IngestFromSingleBlobAsync(this IKustoIngestClient client, BlobDescription blobDescription, bool deleteSourceOnSuccess, KustoIngestionProperties ingestionProperties, long? rawDataSize = null);

    /// <summary>
    /// Ingest data from <see cref="IDataReader"/>, which is closed and disposed of upon call completion
    /// </summary>
    /// <param name="client">The ingest client that will execute the ingestions</param>
    /// <param name="dataReader">The data to ingest (only the first record set will be used)</param>
    /// <param name="ingestionProperties">Additional properties to be used during the ingestion process</param>
    /// <returns><see cref="IKustoIngestionResult"/></returns>
    public static IKustoIngestionResult IngestFromDataReader(this IKustoIngestClient client, IDataReader dataReader, KustoIngestionProperties ingestionProperties);

    /// <summary>
    ///  Asynchronously ingest data from <see cref="IDataReader"/>, which is closed and disposed of upon call completion
    /// </summary>
    /// <param name="client">The ingest client that will execute the ingestions</param>
    /// <param name="dataReader">The data to ingest (only the first record set will be used)</param>
    /// <param name="ingestionProperties">Additional properties to be used during the ingestion process</param>
    /// <returns>An <see cref="IKustoIngestionResult"/> task</returns>
    public static Task<IKustoIngestionResult> IngestFromDataReaderAsync(this IKustoIngestClient client, IDataReader dataReader, KustoIngestionProperties ingestionProperties);

    /// <summary>
    /// Ingest data from <see cref="IDataReader"/>, which is closed and disposed of upon call completion
    /// </summary>
    /// <param name="client">The ingest client that will execute the ingestions</param>
    /// <param name="dataReaderDescription"><see cref="DataReaderDescription"/>Represents the data to ingest (only the first record set will be used)</param>
    /// <param name="ingestionProperties">Additional properties to be used during the ingestion process</param>
    /// <returns><see cref="IKustoIngestionResult"/></returns>
    public static IKustoIngestionResult IngestFromDataReader(this IKustoIngestClient client, DataReaderDescription dataReaderDescription, KustoIngestionProperties ingestionProperties);

    /// <summary>
    ///  Asynchronously ingest data from <see cref="IDataReader"/>, which is closed and disposed of upon call completion
    /// </summary>
    /// <param name="client">The ingest client that will execute the ingestions</param>
    /// <param name="dataReaderDescription"><see cref="DataReaderDescription"/>Represents the data to ingest (only the first record set will be used)</param>
    /// <param name="ingestionProperties">Additional properties to be used during the ingestion process</param>
    /// <returns>An <see cref="IKustoIngestionResult"/> task</returns>
    public static Task<IKustoIngestionResult> IngestFromDataReaderAsync(this IKustoIngestClient client, DataReaderDescription dataReaderDescription, KustoIngestionProperties ingestionProperties);

    /// <summary>
    /// Ingest data from a single file
    /// </summary>
    /// <param name="client">The ingest client that will execute the ingestions</param>
    /// <param name="filePath">Absolute path of the source file to be ingested</param>
    /// <param name="deleteSourceOnSuccess">Indicates if the source file should be deleted after a successful ingestion</param>
    /// <param name="ingestionProperties">Additional properties to be used during the ingestion process</param>
    /// <returns><see cref="IKustoIngestionResult"/></returns>
    public static IKustoIngestionResult IngestFromSingleFile(this IKustoIngestClient client, string filePath, bool deleteSourceOnSuccess, KustoIngestionProperties ingestionProperties);

    /// <summary>
    /// Ingest data from a single file asynchronously
    /// </summary>
    /// <param name="client">The ingest client that will execute the ingestions</param>
    /// <param name="filePath">Absolute path of the source file to be ingested</param>
    /// <param name="deleteSourceOnSuccess">Indicates if the source file should be deleted after a successful ingestion</param>
    /// <param name="ingestionProperties">Additional properties to be used during the ingestion process</param>
    /// <returns>An <see cref="IKustoIngestionResult"/> task</returns>
    public static Task<IKustoIngestionResult> IngestFromSingleFileAsync(this IKustoIngestClient client, string filePath, bool deleteSourceOnSuccess, KustoIngestionProperties ingestionProperties);

    /// <summary>
    /// Ingest data from a single file
    /// </summary>
    /// <param name="client">The ingest client that will execute the ingestions</param>
    /// <param name="fileDescription"><see cref="FileDescription"/> representing the file that will be ingested</param>
    /// <param name="deleteSourceOnSuccess">Indicates if the source file should be deleted after a successful ingestion</param>
    /// <param name="ingestionProperties">Additional properties to be used during the ingestion process</param>
    /// <returns><see cref="IKustoIngestionResult"/></returns>
    public static IKustoIngestionResult IngestFromSingleFile(this IKustoIngestClient client, FileDescription fileDescription, bool deleteSourceOnSuccess, KustoIngestionProperties ingestionProperties);

    /// <summary>
    /// Ingest data from a single file asynchronously
    /// </summary>
    /// <param name="client">The ingest client that will execute the ingestions</param>
    /// <param name="fileDescription"><see cref="FileDescription"/> representing the file that will be ingested</param>
    /// <param name="deleteSourceOnSuccess">Indicates if the source file should be deleted after a successful ingestion</param>
    /// <param name="ingestionProperties">Additional properties to be used during the ingestion process</param>
    /// <returns>An <see cref="IKustoIngestionResult"/> task</returns>
    public static Task<IKustoIngestionResult> IngestFromSingleFileAsync(this IKustoIngestClient client, FileDescription fileDescription, bool deleteSourceOnSuccess, KustoIngestionProperties ingestionProperties);

    /// <summary>
    /// Ingest data from <see cref="Stream"/>
    /// </summary>
    /// <param name="client">The ingest client that will execute the ingestions</param>
    /// <param name="stream">The data to ingest</param>
    /// <param name="ingestionProperties">Additional properties to be used during the ingestion process</param>
    /// <param name="leaveOpen">Optional. If set to 'false' (default value), <paramref name="stream"/> will be closed and disposed on call completion</param>
    /// <returns><see cref="IKustoIngestionResult"/></returns>
    public static IKustoIngestionResult IngestFromStream(this IKustoIngestClient client, Stream stream, KustoIngestionProperties ingestionProperties, bool leaveOpen = false);

    /// <summary>
    /// Ingest data from <see cref="Stream"/> asynchronously
    /// </summary>
    /// <param name="client">The ingest client that will execute the ingestions</param>
    /// <param name="stream">The data to ingest</param>
    /// <param name="ingestionProperties">Additional properties to be used during the ingestion process</param>
    /// <param name="leaveOpen">Optional. If set to 'false' (default value), <paramref name="stream"/> will be closed and disposed on call completion</param>
    /// <returns>An <see cref="IKustoIngestionResult"/> task</returns>
    public static Task<IKustoIngestionResult> IngestFromStreamAsync(this IKustoIngestClient client, Stream stream, KustoIngestionProperties ingestionProperties, bool leaveOpen = false);

    /// <summary>
    /// Ingest data from <see cref="Stream"/>
    /// </summary>
    /// <param name="client">The ingest client that will execute the ingestions</param>
    /// <param name="streamDescription"><see cref="StreamDescription"/>Represents the data to ingest</param>
    /// <param name="ingestionProperties">Additional properties to be used during the ingestion process</param>
    /// <param name="leaveOpen">Optional. If set to 'false' (default value), streamDescription.Stream will be closed and disposed on call completion</param>
    /// <returns><see cref="IKustoIngestionResult"/></returns>
    public static IKustoIngestionResult IngestFromStream(this IKustoIngestClient client, StreamDescription streamDescription, KustoIngestionProperties ingestionProperties, bool leaveOpen = false);

    /// <summary>
    /// Ingest data from <see cref="Stream"/> asynchronously
    /// </summary>
    /// <param name="client">The ingest client that will execute the ingestions</param>
    /// <param name="streamDescription"><see cref="StreamDescription"/>Represents the data to ingest</param>
    /// <param name="ingestionProperties">Additional properties to be used during the ingestion process</param>
    /// <param name="leaveOpen">Optional. If set to 'false' (default value), streamDescription.Stream will be closed and disposed on call completion</param>
    /// <returns>An <see cref="IKustoIngestionResult"/> task</returns>
    public static Task<IKustoIngestionResult> IngestFromStreamAsync(this IKustoIngestClient client, StreamDescription streamDescription, KustoIngestionProperties ingestionProperties, bool leaveOpen = false);
}
```

## <a name="class-kustoingestfactory"></a>클래스 KustoIngestFactory

* CreateDirectIngestClient
* CreateQueuedIngestClient
* CreateManagedStreamingIngestClient
* CreateStreamingIngestClient

```csharp
/// <summary>
/// Factory for creating Kusto ingestion objects.
/// </summary>
public static class KustoIngestFactory
{
    /// <summary>
    /// Creates an implementation of <see cref="IKustoIngestClient"/> that communicates
    /// directly with the Kusto engine service.
    /// </summary>
    /// <param name="kcsb">Indicates the connection to the Kusto engine service.</param>
    /// <returns>An implementation of <see cref="IKustoIngestClient"/> that communicates
    /// directly with the Kusto engine service.</returns>
    /// <remarks>In most cases, it is preferred that ingestion be done using the
    /// queued implementation of <see cref="IKustoIngestClient"/>. See <see cref="CreateQueuedIngestClient(KustoConnectionStringBuilder)"/>.</remarks>
    public static IKustoIngestClient CreateDirectIngestClient(KustoConnectionStringBuilder kcsb);

    /// <summary>
    /// Creates an implementation of <see cref="IKustoIngestClient"/> that communicates
    /// directly with the Kusto engine service.
    /// </summary>
    /// <param name="connectionString">Indicates the connection to the Kusto engine service.</param>
    /// <returns>An implementation of <see cref="IKustoIngestClient"/> that communicates
    /// directly with the Kusto engine service.</returns>
    /// <remarks>In most cases, it is preferred that ingestion be done using the
    /// queued implementation of <see cref="IKustoIngestClient"/>. See <see cref="CreateQueuedIngestClient(string)"/>.</remarks>
    public static IKustoIngestClient CreateDirectIngestClient(string connectionString);

    /// <summary>
    /// Creates an implementation of <see cref="IKustoQueuedIngestClient"/> that communicates
    /// with the Kusto ingestion service using a reliable queue.
    /// </summary>
    /// <param name="kcsb">Indicates the connection to the Kusto ingestion service.
    /// Note that the ingestion service generally has a "ingest-" prefix in the
    /// DNS host name part.</param>
    /// <returns>An implementation of <see cref="IKustoQueuedIngestClient"/> that communicates
    /// with the Kusto ingestion service using a reliable queue.</returns>
    public static IKustoQueuedIngestClient CreateQueuedIngestClient(KustoConnectionStringBuilder kcsb);

    /// <summary>
    /// Creates an implementation of <see cref="IKustoQueuedIngestClient"/> that communicates
    /// with the Kusto ingestion service using a reliable queue.
    /// </summary>
    /// <param name="connectionString">Indicates the connection to the Kusto ingestion service.
    /// Note that the ingestion service generally has a "ingest-" prefix in the
    /// DNS host name part.</param>
    /// <returns>An implementation of <see cref="IKustoQueuedIngestClient"/> that communicates with the Kusto ingestion service using a reliable queue.</returns>
    public static IKustoQueuedIngestClient CreateQueuedIngestClient(string connectionString);

    /// <summary>
    /// Creates an implementation of <see cref="IKustoIngestClient"/> that performs managed streaming ingestion
    /// </summary>
    /// <param name="engineKcsb">Indicates the connection to the Kusto engine service.</param>
    /// <param name="dmKcsb">Indicates the connection to the Kusto data management service.</param>
    /// <returns>An implementation of <see cref="IKustoIngestClient"/> that performs managed streaming ingestion</returns>
    /// <remarks>Streaming ingestion is performed directy intto Kusto enginge cluster 
    /// and is optimized for low-latency ingestion of relatively small chunks of data.
    /// If the streaming ingset doesn't succeed after several retries, queued ingestion will be performed.</remarks>
    public static IKustoIngestClient CreateManagedStreamingIngestClient(KustoConnectionStringBuilder engineKcsb, KustoConnectionStringBuilder dmKcsb);

    /// <summary>
    /// Creates an implementation of <see cref="IKustoIngestClient"/> that performs managed streaming ingestion
    /// </summary>
    /// <param name="engineConnectionString">Indicates the connection to the Kusto engine service.</param>
    /// <param name="dmConnectionString">Indicates the connection to the Kusto data management service.</param>
    /// <returns>An implementation of <see cref="IKustoIngestClient"/> that performs managed streaming ingestion</returns>
    /// <remarks>Streaming ingestion is performed directy intto Kusto enginge cluster 
    /// and is optimized for low-latency ingestion of relatively small chunks of data.
    /// If the streaming ingset doesn't succeed after several retries, queued ingestion will be performed.</remarks>
    public static IKustoIngestClient CreateManagedStreamingIngestClient(string engineConnectionString, string dmConnectionString);

    /// <summary>
    /// Creates an implementation of <see cref="IKustoIngestClient"/> that performs streaming ingestion
    /// </summary>
    /// <param name="kcsb">Indicates the connection to the Kusto engine service.</param>
    /// <returns>An implementation of <see cref="IKustoIngestClient"/> that performs streaming ingestion</returns>
    /// <remarks>Streaming ingestion is performed directy intto Kusto enginge cluster 
    /// and is optimized for low-latency ingestion of relatively small chunks of data</remarks>
    public static IKustoIngestClient CreateStreamingIngestClient(KustoConnectionStringBuilder kcsb);

    /// <summary>
    /// Creates an implementation of <see cref="IKustoIngestClient"/> that performs streaming ingestion
    /// </summary>
    /// <param name="connectionString">Indicates the connection to the Kusto engine service.</param>
    /// <returns>An implementation of <see cref="IKustoIngestClient"/> that performs streaming ingestion</returns>
    /// <remarks>Streaming ingestion is performed directy into Kusto enginge cluster 
    /// and is optimized for low-latency ingestion of relatively small chunks of data</remarks>
    public static IKustoIngestClient CreateStreamingIngestClient(string connectionString);
}
```

## <a name="class-kustoingestionproperties"></a>클래스 KustoIngestionProperties

KustoIngestionProperties 클래스에는 수집 프로세스를 세부적으로 제어 하는 기본 수집 속성과 Kusto engine에서이를 처리 하는 방법이 포함 되어 있습니다.

|속성   |의미    |
|-----------|-----------|
|DatabaseName |수집할 데이터베이스의 이름입니다. |
|TableName |수집할 테이블의 이름 |
|DropByTags |각 범위에 포함 되는 태그입니다. DropByTags는 영구적 이며 다음과 같이 사용할 수 있습니다. `.show table T extents where tags has 'some tag'` 또는`.drop extents <| .show table T extents where tags has 'some tag'` |
|IngestByTags |익스텐트에 따라 작성 된 태그입니다. 나중에 속성을 사용 `IngestIfNotExists` 하 여 동일한 데이터를 두 번 수집 않도록 할 수 있습니다. |
|AdditionalTags |필요에 따라 추가 태그 |
|IngestIfNotExists |다시 수집 하지 않으려는 태그 목록 (테이블당) |
|CSVMapping |각 열에 대해 데이터 형식 및 서 수 열 번호를 정의 합니다. CSV 수집에만 해당 됩니다 (선택 사항). |
|JsonMapping |각 열에 대해 JSON 경로 및 변환 옵션을 정의 합니다. **JSON 수집에 필수** |
|AvroMapping |각 열에 대해 Avro 레코드의 필드 이름을 정의 합니다. **AVRO 수집에 필수** |
|ValidationPolicy |데이터 유효성 검사 정의. 자세한 내용은 [TODO]를 참조 하십시오. |
|형식 |수집 되는 데이터의 형식입니다. |
|AdditionalProperties | 수집 [속성](https://docs.microsoft.com/azure/data-explorer/ingestion-properties) 으로 수집 명령에 전달 되는 기타 속성입니다. 수집 속성이이 클래스의 개별 멤버에 표시 되지 않기 때문에 속성이 전달 됩니다.|

```csharp
public class KustoIngestionProperties
{
    public string DatabaseName { get; set; }
    public string TableName { get; set; }
    public IEnumerable<string> DropByTags { get; set; }
    public IEnumerable<string> IngestByTags { get; set; }
    public IEnumerable<string> AdditionalTags { get; set; }
    public IEnumerable<string> IngestIfNotExists { get; set; }
    public IEnumerable<CsvColumnMapping> CSVMapping { get; set; }
    public IEnumerable<JsonColumnMapping> JsonMapping { get; set; } // Must be set for DataSourceFormat.json format
    public IEnumerable<AvroColumnMapping> AvroMapping { get; set; } // Must be set for DataSourceFormat.avro format
    public ValidationPolicy ValidationPolicy { get; set; }
    public DataSourceFormat? Format { get; set; }
    public bool IgnoreSizeLimit { get; set; } // Determines whether the limit of 4GB per single ingestion source should be ignored. Defaults to false.
    public IDictionary<string, string> AdditionalProperties { get; set; }

    public KustoIngestionProperties(string databaseName, string tableName);
}
```

## <a name="class-jsoncolumnmapping"></a>클래스 JsonColumnMapping

```csharp
public class JsonColumnMapping
{
    /// The column name (in the Kusto table)
    public string ColumnName { get; set; }

    /// The JsonPath to the desired property in the JSON document
    public string JsonPath { get; set; }
}
```

## <a name="class-csvcolumnmapping"></a>클래스 CsvColumnMapping

```csharp
public class CsvColumnMapping
{
    /// The column name (in the Kusto table)
    public string ColumnName { get; set; }

    /// The column's data type in the table (CSL term), if empty, the current column data type will be used.
    /// If column doesn't exist, a new one will be created (alter table) with this data type, if empty, StorageDataType.StringBuffer will be used.
    public string CslDataType { get; set; }

    /// The CSV column dataType (not in use for now)
    public string CsvColumnDataType { get; set; }

    /// CSV ordinal number
    public int Ordinal { get; set; }

    /// This column has a const value (the Ordinal field is ignored, if this value is not null or empty)
    public string ConstValue { get; set; }
}
```

## <a name="enum-datasourceformat"></a>DataSourceFormat 열거형

```csharp
public enum DataSourceFormat
{
    csv,        // Data is in a CSV(-comma-separated values) format
    tsv,        // Data is in a TSV(-tab-separated values) format
    scsv,       // Data is in a SCSV(-semicolon-separated values) format
    sohsv,      // Data is in a SOHSV(-SOH (ASCII 1) separated values) format
    psv,        // Data is in a PSV (pipe-separated values) format
    txt,        // Each record is a line and has just one field
    raw,        // The entire stream/file/blob is a single record having a single field
    json,       // Data is in a JSON-line format (each line is record with a single JSON value)
    multijson,  // The data stream is a concatenation of JSON documents (property bags all)
    avro,       // Data is in a AVRO format
    parquet,    // Data is in a Parquet format
}
```


## <a name="example-of-kustoingestionproperties-definition"></a>KustoIngestionProperties 정의의 예

```csharp
var guid = new Guid().ToString();
var kustoIngestionProperties = new KustoIngestionProperties("TargetDatabase", "TargetTable")
{
    DropByTags = new List<string> { DateTime.Today.ToString() },
    IngestByTags = new List<string> { guid },
    AdditionalTags = new List<string> { "some tags" },
    IngestIfNotExists = new List<string> { guid },
    CSVMapping = new List<CsvColumnMapping> { new CsvColumnMapping { ColumnName = "columnA", CslDataType = "Dynamic", Ordinal = 1 } },
    JsonMapping = new List<JsonColumnMapping> { new JsonColumnMapping { ColumnName = "columnA" , JsonPath = "$.path" } }, // You can only one of CSV/JSON/AVRO mappings
    AvroMapping = new List<AvroColumnMapping> { new AvroColumnMapping { ColumnName = "columnA" , FieldName = "AvroFieldName" } }, // You can only one of CSV/JSON/AVRO mappings
    ValidationPolicy = new ValidationPolicy { ValidationImplications = ValidationImplications.Fail, ValidationOptions = ValidationOptions.ValidateCsvInputConstantColumns },
    Format = DataSourceFormat.csv
};
```

## <a name="interface-ikustoqueuedingestclient"></a>인터페이스 IKustoQueuedIngestClient

IKustoQueuedIngestClient 인터페이스는 수집 작업 결과를 따르고 수집 클라이언트에 대해 RetryPolicy를 노출 하는 추적 메서드를 추가 합니다.

* PeekTopIngestionFailures
* GetAndDiscardTopIngestionFailures
* GetAndDiscardTopIngestionSuccesses

```csharp
public interface IKustoQueuedIngestClient : IKustoIngestClient
{
    /// <summary>
    /// Peeks top (== oldest) ingestion failures  
    /// </summary>
    /// <param name="messagesLimit">Maximum ingestion failures to peek. Default value peeks 32 messages.</param>
    /// <returns>A task which its result contains IEnumerable of <see cref="IngestionFailure"/>. The received messages won't be discarded from the relevant azure queue.</returns>
    Task<IEnumerable<IngestionFailure>> PeekTopIngestionFailures(int messagesLimit = -1);

    /// <summary>
    /// Returns and deletes top (== oldest) ingestion failure notifications 
    /// </summary>
    /// <param name="messagesLimit">Maximum ingestion failure notifications to get. Default value peeks 32 messages.</param>
    /// <returns>A task which its result contains IEnumerable of <see cref="IngestionFailure"/>. The received messages will be discarded from the relevant azure queue.</returns>
    Task<IEnumerable<IngestionFailure>> GetAndDiscardTopIngestionFailures(int messagesLimit = -1);

    /// <summary>
    /// Returns and deletes top (== oldest) ingestion success notifications 
    /// </summary>
    /// <param name="messagesLimit">Maximum ingestion success notifications to get. Default value peeks 32 messages.</param>
    /// <returns>A task which its result contains IEnumerable of <see cref="IngestionSuccess"/>. The received messages will be discarded from the relevant azure queue.</returns>
    Task<IEnumerable<IngestionSuccess>> GetAndDiscardTopIngestionSuccesses(int messagesLimit = -1);

    /// <summary>
    /// An implementation of IRetryPolicy that will be enforced on every ingest call,
    /// which affects how the ingest client handles retrying on transient failures 
    /// </summary>
    IRetryPolicy QueueRetryPolicy { get; set; }
}
```

## <a name="class-kustoqueuedingestionproperties"></a>클래스 KustoQueuedIngestionProperties

KustoQueuedIngestionProperties 클래스는 수집 동작을 미세 조정 하는 데 사용할 수 있는 여러 컨트롤 노브를 사용 하 여 KustoIngestionProperties를 확장 합니다.

|속성   |의미    |
|-----------|-----------|
|즉시 flush |기본값은 `false`입니다. 로 설정 `true` 된 경우 데이터 관리 서비스의 집계 메커니즘을 무시 합니다. |
|IngestionReportLevel |수집 상태 보고 수준을 제어 합니다 (기본값 `FailuresOnly` ). 성능 및 저장소를 적절 하 게 사용 하려면 IngestionReportLevel을로 설정 하지 않는 것이 좋습니다.`FailuresAndSuccesses` |
|IngestionReportMethod |수집 상태 보고의 대상을 제어 합니다. 사용할 수 있는 옵션은 Azure 큐, Azure 테이블 또는 둘 다입니다. 기본값은 `Queue`입니다.

```csharp
public class KustoQueuedIngestionProperties : KustoIngestionProperties
{
    /// <summary>
    /// Allows to stop the batching phase and will cause to an immediate ingestion.
    /// Defaults to 'false'. 
    /// </summary>
    public bool FlushImmediately { get; set; }

    /// <summary>
    /// Controls the ingestion status report level.
    /// Defaults to 'FailuresOnly'.
    /// </summary>
    public IngestionReportLevel ReportLevel { get; set; }

    /// <summary>
    /// Controls the target of the ingestion status reporting. Available options are Azure Queue, Azure Table, or both.
    /// Defaults to 'Queue'.
    /// </summary>
    public IngestionReportMethod ReportMethod;

    public KustoQueuedIngestionProperties(string databaseName, string tableName);
}
```
