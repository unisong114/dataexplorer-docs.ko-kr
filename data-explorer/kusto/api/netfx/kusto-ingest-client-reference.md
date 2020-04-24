---
title: Kusto.Ingest 클라이언트 참조 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기에서 Kusto.Ingest 클라이언트 참조에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: e5a9c1fa561fa07df527f17552a3a8f594a4e5d8
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81503114"
---
# <a name="kustoingest-client-reference"></a>Kusto.Ingest 클라이언트 참조

Kusto.Ingest 라이브러리의 주요 인터페이스 및 공장 클래스는 다음과 같습니다.

* [인터페이스 IKustoIngest클라이언트](#interface-ikustoingestclient): 주요 섭취 인터페이스.
* [클래스 확장KustoIngestClient](#class-extendedkustoingestclient): 기본 구성 인터페이스에 대한 확장.
* [클래스 KustoIngest공장](#class-kustoingestfactory): 섭취 클라이언트를위한 주요 공장.
* [클래스 KustoIngestion속성](#class-kustoingestionproperties): 공통 섭취 속성을 제공하는 데 사용되는 클래스입니다.
* [클래스 JsonColumnMapping](#class-jsoncolumnmapping): JSON 데이터 원본에서 수집할 때 적용할 스키마 매핑을 설명하는 데 사용되는 클래스입니다.
* [클래스 CsvColumnMapping](#class-csvcolumnmapping): CSV 데이터 원본에서 수집할 때 적용할 스키마 매핑을 설명하는 데 사용되는 클래스입니다.
* [열거형 데이터 소스 형식](#enum-datasourceformat): 지원되는 데이터 원본 형식(예: CSV, JSON)
* [인터페이스 IKustoQueuedingestClient](#interface-ikustoqueuedingestclient): 큐에 대기된 인에대해서만 적용되는 작업을 설명하는 인터페이스입니다.
* [클래스 KustoQueuedingestion](#class-kustoqueuedingestionproperties)속성 : 큐에 대기된 인에만 적용되는 속성입니다.

## <a name="interface-ikustoingestclient"></a>인터페이스 IKustoIngest클라이언트

* 인제스트From데이터리더Async
* 인제스트From스토리지동기
* 인제스트From스트림Async

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

## <a name="class-extendedkustoingestclient"></a>클래스 확장쿠스토잉스트클라이언트

* 인제스트에서싱글블B - 더 이상 사용되지 않습니다. 대신 `IKustoIngestClient.IngestFromStorageAsync`를 사용하십시오.
* 인제스트에서싱글블로브Async - 더 이상 사용되지 않습니다. 대신 `IKustoIngestClient.IngestFromStorageAsync`를 사용하십시오.
* 인제스트From데이터리더 - 더 이상 사용되지 않습니다. 대신 `IKustoIngestClient.IngestFromDataReaderAsync`를 사용하십시오.
* 인제스트From데이터리더Async
* 인제스트From싱글파일 - 더 이상 사용되지 않습니다. 대신 `IKustoIngestClient.IngestFromStorageAsync`를 사용하십시오.
* 인제스트From싱글파일Async - 더 이상 사용되지 않습니다. 대신 `IKustoIngestClient.IngestFromStorageAsync`를 사용하십시오.
* FromStream에서 인제스트 - 더 이상 사용되지 않습니다. 대신 `IKustoIngestClient.IngestFromStreamAsync`를 사용하십시오.
* 인제스트From스트림Async

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

## <a name="class-kustoingestfactory"></a>클래스 쿠스토잉제스트팩토리

* 생성직접 작성 클라이언트
* 큐어작성 클라이언트 만들기
* 관리되는 스트리밍 클라이언트 만들기
* 생성 스트리밍 게스트클라이언트

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

## <a name="class-kustoingestionproperties"></a>클래스 쿠스토인션프로퍼티

KustoIngestionProperties 클래스는 Kusto 엔진에 의한 섭취 프로세스 및 처리를 정밀하게 제어할 수 있는 기본 섭취 특성을 캡슐화합니다.

|속성   |의미    |
|-----------|-----------|
|DatabaseName |인더잉할 데이터베이스 이름 |
|TableName |섭취할 테이블 이름 |
|드롭바이태그 |각 익스텐트는 태그를 갖습니다. DropByTags는 영구적이며 다음과 같이 `.show table T extents where tags has 'some tag'` 사용할 수 있습니다.`.drop extents <| .show table T extents where tags has 'some tag'` |
|인제스트비태그 |익스텐트에 따라 작성된 태그입니다. 나중에 동일한 데이터를 `IngestIfNotExists` 두 번 수집하지 않도록 속성과 함께 사용할 수 있습니다. |
|추가 태그 |필요에 따라 추가 태그 |
|인제스트IfNotExists |다시 원하지 않는 태그 목록(표당) |
|CSV매핑 |각 열에 대해 데이터 형식과 서수 열 번호를 정의합니다. CSV 사용과 관련이 있습니다(선택 사항) |
|Json매핑 |각 열에 대해 JSON 경로 및 변환 옵션을 정의합니다. **JSON 섭취에 필수** |
|아브로 매핑 |각 열에 대해 Avro 레코드에서 필드 이름을 정의합니다. **AVRO 섭취에 필수** |
|유효성 검사정책 |데이터 유효성 검사 정의. 자세한 내용은 [TODO]를 참조하십시오. |
|형식 |수집중인 데이터의 형식 |
|추가 속성 | 모든 인기 속성이 이 클래스의 별도 멤버에 표시되지 않기 때문에 [인기 속성으로](https://docs.microsoft.com/azure/data-explorer/ingestion-properties) 전달되는 다른 속성은 인기 명령으로 전달됩니다.|

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

## <a name="class-jsoncolumnmapping"></a>클래스 Json열 매핑

```csharp
public class JsonColumnMapping
{
    /// The column name (in the Kusto table)
    public string ColumnName { get; set; }

    /// The JsonPath to the desired property in the JSON document
    public string JsonPath { get; set; }
}
```

## <a name="class-csvcolumnmapping"></a>클래스 Csv열 매핑

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

## <a name="enum-datasourceformat"></a>열거형 데이터 소스 형식

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


## <a name="example-of-kustoingestionproperties-definition"></a>쿠스토인션속성 정의의 예

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

## <a name="interface-ikustoqueuedingestclient"></a>인터페이스 IKustoQueuedingest클라이언트

IKustoQueuedIngestClient 인터페이스는 인기 작업 결과를 따르는 추적 메서드를 추가하고 인제스트 클라이언트에 대한 RetryPolicy를 노출합니다.

* 픽탑링실패
* GetandDiscardTopingestion실패
* GetAnd폐기탑화성공

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

## <a name="class-kustoqueuedingestionproperties"></a>클래스 쿠스토큐디징프로퍼티

KustoQueuedingestionProperties 클래스는 섭취 동작을 미세 조정하는 데 사용할 수있는 여러 컨트롤 노브를 사용하여 KustoIngestionProperties를 확장합니다.

|속성   |의미    |
|-----------|-----------|
|플러시즉시 |기본값은 `false`입니다. `true`을 설정하면 데이터 관리 서비스의 집계 메커니즘을 우회합니다. |
|인제션보고서수준 |인스티온 상태 보고 수준을 제어합니다(기본값으로). `FailuresOnly` 성능 및 저장소 사용량 측면에서 는 IngestionReportLevel을`FailuresAndSuccesses` |
|인제션보고서방법 |인기 상태 보고의 대상을 제어합니다. 사용 가능한 옵션은 Azure 큐, Azure 테이블 또는 둘 다입니다. 기본값은 `Queue`입니다.

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