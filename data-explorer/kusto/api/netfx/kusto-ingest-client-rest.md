---
title: 수집 라이브러리 없이 kusto 데이터 수집-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 Kusto. 수집 라이브러리 없이 방법 데이터 수집을 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.custom: has-adal-ref
ms.date: 02/19/2020
ms.openlocfilehash: 694b229c36a8bbbe6c15531b555dc8467198cd65
ms.sourcegitcommit: 898f67b83ae8cf55e93ce172a6fd3473b7c1c094
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/21/2020
ms.locfileid: "92337604"
---
# <a name="ingestion-without-kustoingest-library"></a>Kusto 수집 라이브러리 없이 수집

Kusto. 수집 라이브러리는 데이터를 Azure 데이터 탐색기로 수집 하는 데 기본 설정 되어 있습니다. 그러나 Kusto. 수집 패키지에 종속 되지 않고도 거의 동일한 기능을 달성할 수 있습니다.
이 문서에서는 프로덕션 등급 파이프라인에 대 한 Azure 데이터 탐색기에 대 한 *큐* 에 수집을 사용 하 여 방법을 보여 줍니다.

> [!NOTE]
> 아래 코드는 c #으로 작성 되었으며 Azure Storage SDK, ADAL 인증 라이브러리 및 패키지의 NewtonSoft.JS를 사용 하 여 샘플 코드를 간소화 합니다. 필요한 경우 해당 코드를 적절 한 [Azure Storage REST API](/rest/api/storageservices/blob-service-rest-api) 호출, [non-.NET ADAL 패키지](/azure/active-directory/develop/active-directory-authentication-libraries)및 사용 가능한 모든 JSON 처리 패키지로 바꿀 수 있습니다.

이 문서에서는 권장 되는 수집 모드를 다룹니다. Kusto. 수집 라이브러리의 경우 해당 엔터티는 [IKustoQueuedIngestClient](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient) 인터페이스입니다. 여기서 클라이언트 코드는 azure 큐에 수집 알림 메시지를 게시 하 여 Azure 데이터 탐색기 서비스와 상호 작용 합니다. 메시지에 대 한 참조는 Kusto 데이터 관리 (수집) 서비스에서 가져옵니다. 서비스와의 상호 작용은 Azure Active Directory (Azure AD)를 사용 하 여 인증 되어야 합니다.

다음 코드는 kusto 데이터 관리 서비스에서 Kusto. 수집 라이브러리를 사용 하지 않고 대기 중인 데이터 수집을 처리 하는 방법을 보여 줍니다. 이 예제는 환경 또는 기타 제한 사항으로 인해 전체 .NET을 액세스할 수 없거나 사용할 수 없는 경우에 유용할 수 있습니다.

이 코드에는 Azure Storage 클라이언트를 만들고 blob에 데이터를 업로드 하는 단계가 포함 되어 있습니다.
각 단계에 대 한 자세한 내용은 샘플 코드 뒤에 자세히 설명 되어 있습니다.

1. [Azure 데이터 탐색기 수집 서비스에 액세스 하기 위한 인증 토큰 가져오기](#obtain-authentication-evidence-from-azure-ad)
1. Azure 데이터 탐색기 수집 서비스를 쿼리하여 다음을 확인 합니다.
    * [수집 리소스 (큐 및 blob 컨테이너)](#retrieve-azure-data-explorer-ingestion-resources)
    * [모든 수집 메시지에 추가 되는 Kusto id 토큰입니다.](#obtain-a-kusto-identity-token)
1. [Kusto in (2)에서 가져온 blob 컨테이너 중 하나의 blob에 데이터 업로드](#upload-data-to-the-azure-blob-container)
1. [대상 데이터베이스 및 테이블을 식별 하 고의 blob을 가리키는 수집 메시지를 작성 합니다 (3).](#compose-the-azure-data-explorer-ingestion-message)
1. [(4)에서 작성 한 수집 메시지를 Azure 데이터 탐색기 (2)에서 가져온 수집 큐에 게시 합니다.](#post-the-azure-data-explorer-ingestion-message-to-the-azure-data-explorer-ingestion-queue)**
1. [수집 중에 서비스에서 발견 한 오류 검색](#check-for-error-messages-from-the-azure-queue)

```csharp
// A container class for ingestion resources we are going to obtain from Azure Data Explorer
internal class IngestionResourcesSnapshot
{
    public IList<string> IngestionQueues { get; set; } = new List<string>();
    public IList<string> TempStorageContainers { get; set; } = new List<string>();

    public string FailureNotificationsQueue { get; set; } = string.Empty;
    public string SuccessNotificationsQueue { get; set; } = string.Empty;
}

public static void IngestSingleFile(string file, string db, string table, string ingestionMappingRef)
{
    // Your Azure Data Explorer ingestion service URI, typically ingest-<your cluster name>.kusto.windows.net
    string DmServiceBaseUri = @"https://ingest-{serviceNameAndRegion}.kusto.windows.net";

    // 1. Authenticate the interactive user (or application) to access Kusto ingestion service
    string bearerToken = AuthenticateInteractiveUser(DmServiceBaseUri);

    // 2a. Retrieve ingestion resources
    IngestionResourcesSnapshot ingestionResources = RetrieveIngestionResources(DmServiceBaseUri, bearerToken);

    // 2b. Retrieve Kusto identity token
    string identityToken = RetrieveKustoIdentityToken(DmServiceBaseUri, bearerToken);

    // 3. Upload file to one of the blob containers we got from Azure Data Explorer.
    // This example uses the first one, but when working with multiple blobs,
    // one should round-robin the containers in order to prevent throttling
    long blobSizeBytes = 0;
    string blobName = $"TestData{DateTime.UtcNow.ToString("yyyy-MM-dd_HH-mm-ss.FFF")}";
    string blobUriWithSas = UploadFileToBlobContainer(file, ingestionResources.TempStorageContainers.First(),
                                                            "temp001", blobName, out blobSizeBytes);

    // 4. Compose ingestion command
    string ingestionMessage = PrepareIngestionMessage(db, table, blobUriWithSas, blobSizeBytes, ingestionMappingRef, identityToken);

    // 5. Post ingestion command to one of the ingestion queues we got from Azure Data Explorer.
    // This example uses the first one, but when working with multiple blobs,
    // one should round-robin the queues in order to prevent throttling
    PostMessageToQueue(ingestionResources.IngestionQueues.First(), ingestionMessage);

    Thread.Sleep(20000);

    // 6a. Read success notifications
    var successes = PopTopMessagesFromQueue(ingestionResources.SuccessNotificationsQueue, 32);
    foreach (var sm in successes)
    {
        Console.WriteLine($"Ingestion completed: {sm}");
    }

    // 6b. Read failure notifications
    var errors = PopTopMessagesFromQueue(ingestionResources.FailureNotificationsQueue, 32);
    foreach (var em in errors)
    {
        Console.WriteLine($"Ingestion error: {em}");
    }
}
```

## <a name="using-queued-ingestion-to-azure-data-explorer-for-production-grade-pipelines"></a>프로덕션 등급 파이프라인에 대 한 Azure 데이터 탐색기에 대기 중인 수집 사용

### <a name="obtain-authentication-evidence-from-azure-ad"></a>Azure AD에서 인증 증명 정보 가져오기

여기서는 ADAL을 사용 하 여 Kusto 데이터 관리 서비스에 액세스 하 고 해당 입력 큐를 요청 하는 Azure AD 토큰을 가져옵니다.
ADAL은 필요한 경우 [비 Windows 플랫폼](/azure/active-directory/develop/active-directory-authentication-libraries) 에서 사용할 수 있습니다.

```csharp
// Authenticates the interactive user and retrieves Azure AD Access token for specified resource
internal static string AuthenticateInteractiveUser(string resource)
{
    // Create Auth Context for MSFT Azure AD:
    AuthenticationContext authContext = new AuthenticationContext("https://login.microsoftonline.com/{Azure AD Tenant ID or name}");

    // Acquire user token for the interactive user for Azure Data Explorer:
    AuthenticationResult result =
        authContext.AcquireTokenAsync(resource, "<your client app ID>", new Uri(@"<your client app URI>"),
                                        new PlatformParameters(PromptBehavior.Auto), UserIdentifier.AnyUser, "prompt=select_account").Result;
    return result.AccessToken;
}
```

### <a name="retrieve-azure-data-explorer-ingestion-resources"></a>Azure 데이터 탐색기 수집 리소스 검색

수집 리소스의 반환을 요청 하 여 데이터 관리 서비스에 대 한 HTTP POST 요청을 수동으로 생성 합니다. 이러한 리소스에는 DM 서비스가 수신 대기 하는 큐 및 데이터 업로드를 위한 blob 컨테이너가 포함 됩니다.
데이터 관리 서비스는 이러한 큐 중 하나에 도착 하는 수집 요청을 포함 하는 모든 메시지를 처리 합니다.

```csharp
// Retrieve ingestion resources (queues and blob containers) with SAS from specified Azure Data Explorer Ingestion service using supplied Access token
internal static IngestionResourcesSnapshot RetrieveIngestionResources(string ingestClusterBaseUri, string accessToken)
{
    string ingestClusterUri = $"{ingestClusterBaseUri}/v1/rest/mgmt";
    string requestBody = $"{{ \"csl\": \".get ingestion resources\" }}";

    IngestionResourcesSnapshot ingestionResources = new IngestionResourcesSnapshot();

    using (WebResponse response = SendPostRequest(ingestClusterUri, accessToken, requestBody))
    using (StreamReader sr = new StreamReader(response.GetResponseStream()))
    using (JsonTextReader jtr = new JsonTextReader(sr))
    {
        JObject responseJson = JObject.Load(jtr);
        IEnumerable<JToken> tokens;

        // Input queues
        tokens = responseJson.SelectTokens("Tables[0].Rows[?(@.[0] == 'SecuredReadyForAggregationQueue')]");
        foreach (var token in tokens)
        {
            ingestionResources.IngestionQueues.Add((string) token[1]);
        }

        // Temp storage containers
        tokens = responseJson.SelectTokens("Tables[0].Rows[?(@.[0] == 'TempStorage')]");
        foreach (var token in tokens)
        {
            ingestionResources.TempStorageContainers.Add((string)token[1]);
        }

        // Failure notifications queue
        var singleToken =
            responseJson.SelectTokens("Tables[0].Rows[?(@.[0] == 'FailedIngestionsQueue')].[1]").FirstOrDefault();
        ingestionResources.FailureNotificationsQueue = (string)singleToken;

        // Success notifications queue
        singleToken =
            responseJson.SelectTokens("Tables[0].Rows[?(@.[0] == 'SuccessfulIngestionsQueue')].[1]").FirstOrDefault();
        ingestionResources.SuccessNotificationsQueue = (string)singleToken;
    }

    return ingestionResources;
}

// Executes a POST request on provided URI using supplied Access token and request body
internal static WebResponse SendPostRequest(string uriString, string authToken, string body)
{
    WebRequest request = WebRequest.Create(uriString);

    request.Method = "POST";
    request.ContentType = "application/json";
    request.ContentLength = body.Length;
    request.Headers.Set(HttpRequestHeader.Authorization, $"Bearer {authToken}");

    Stream bodyStream = request.GetRequestStream();
    using (StreamWriter sw = new StreamWriter(bodyStream))
    {
        sw.Write(body);
        sw.Flush();
    }

    bodyStream.Close();
    return request.GetResponse();
}
```

### <a name="obtain-a-kusto-identity-token"></a>Kusto id 토큰 가져오기

수집 메시지는 비 직접적인 채널 (Azure 큐)을 통해 Azure 데이터 탐색기에 전달 되므로 Azure 데이터 탐색기 수집 서비스에 액세스 하기 위해 대역 외 권한 부여 유효성 검사를 수행할 수 없습니다. 해결 방법은 모든 수집 메시지에 id 토큰을 연결 하는 것입니다. 토큰은 대역내 권한 부여 유효성 검사를 사용 하도록 설정 합니다. 그러면 수집 메시지를 받을 때 Azure 데이터 탐색기 서비스에서이 서명 된 토큰의 유효성을 검사할 수 있습니다.

```csharp
// Retrieves a Kusto identity token that will be added to every ingest message
internal static string RetrieveKustoIdentityToken(string ingestClusterBaseUri, string accessToken)
{
    string ingestClusterUri = $"{ingestClusterBaseUri}/v1/rest/mgmt";
    string requestBody = $"{{ \"csl\": \".get kusto identity token\" }}";
    string jsonPath = "Tables[0].Rows[*].[0]";

    using (WebResponse response = SendPostRequest(ingestClusterUri, accessToken, requestBody))
    using (StreamReader sr = new StreamReader(response.GetResponseStream()))
    using (JsonTextReader jtr = new JsonTextReader(sr))
    {
        JObject responseJson = JObject.Load(jtr);
        JToken identityToken = responseJson.SelectTokens(jsonPath).FirstOrDefault();

        return ((string)identityToken);
    }
}
```

### <a name="upload-data-to-the-azure-blob-container"></a>Azure Blob 컨테이너에 데이터 업로드

이 단계에서는 수집을 위해 전달 되는 Azure Blob에 로컬 파일을 업로드 하는 방법에 대해 알아봅니다. 이 코드는 Azure Storage SDK를 사용 합니다. 종속성을 사용할 수 없는 경우 [Azure Blob Service REST API](/rest/api/storageservices/fileservices/blob-service-rest-api)를 사용 하 여 구현할 수 있습니다.

```csharp
// Uploads a single local file to an Azure Blob container, returns blob URI and original data size
internal static string UploadFileToBlobContainer(string filePath, string blobContainerUri, string containerName, string blobName, out long blobSize)
{
    var blobUri = new Uri(blobContainerUri);
    CloudBlobContainer blobContainer = new CloudBlobContainer(blobUri);
    CloudBlockBlob blockBlob = blobContainer.GetBlockBlobReference(blobName);

    using (Stream stream = File.OpenRead(filePath))
    {
        blockBlob.UploadFromStream(stream);
        blobSize = blockBlob.Properties.Length;
    }

    return string.Format("{0}{1}", blockBlob.Uri.AbsoluteUri, blobUri.Query);
}
```

### <a name="compose-the-azure-data-explorer-ingestion-message"></a>Azure 데이터 탐색기 수집 메시지 작성

패키지에 대 한 NewtonSoft.JS는 대상 데이터베이스 및 테이블을 식별 하는 유효한 수집 요청을 다시 작성 하며이는 blob을 가리킵니다.
이 메시지는 관련 Kusto 데이터 관리 서비스가 수신 대기 하는 Azure 큐에 게시 됩니다.

다음은 고려해 야 할 몇 가지 사항입니다.

* 이 요청은 수집 메시지의 최소 최소값입니다.

> [!NOTE]
> Id 토큰은 필수 이며 **AdditionalProperties** JSON 개체의 일부 여야 합니다.

* 필요한 경우 CsvMapping 또는 JsonMapping 속성도 제공 해야 합니다.
* 자세한 내용은 수집 [매핑 미리 만들기 문서](../../management/create-ingestion-mapping-command.md)를 참조 하세요.
* 섹션 수집 [메시지 내부 구조](#ingestion-message-internal-structure) 는 수집 메시지 구조에 대 한 설명을 제공 합니다.

```csharp
internal static string PrepareIngestionMessage(string db, string table, string dataUri, long blobSizeBytes, string mappingRef, string identityToken)
{
    var message = new JObject();

    message.Add("Id", Guid.NewGuid().ToString());
    message.Add("BlobPath", dataUri);
    message.Add("RawDataSize", blobSizeBytes);
    message.Add("DatabaseName", db);
    message.Add("TableName", table);
    message.Add("RetainBlobOnSuccess", true);   // Do not delete the blob on success
    message.Add("FlushImmediately", true);      // Do not aggregate
    message.Add("ReportLevel", 2);              // Report failures and successes (might incur perf overhead)
    message.Add("ReportMethod", 0);             // Failures are reported to an Azure Queue

    message.Add("AdditionalProperties", new JObject(
                                            new JProperty("authorizationContext", identityToken),
                                            new JProperty("jsonMappingReference", mappingRef),
                                            // Data is in JSON format
                                            new JProperty("format", "json")));
    return message.ToString();
}
```

### <a name="post-the-azure-data-explorer-ingestion-message-to-the-azure-data-explorer-ingestion-queue"></a>Azure 데이터 탐색기 수집 큐에 Azure 데이터 탐색기 수집 메시지 게시

마지막으로, 생성 한 메시지를 Azure 데이터 탐색기에서 가져온 선택 된 수집 큐에 게시 합니다.

> [!NOTE]
> V12 아래의 .net storage 클라이언트 버전은 기본적으로 메시지를 base64로 인코드 합니다. 자세한 내용은 [저장소 문서](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.encodemessage?view=azure-dotnet-legacy#Microsoft_WindowsAzure_Storage_Queue_CloudQueue_EncodeMessage)를 참조 하세요. V12 위의 .Net storage 클라이언트 버전을 사용 하는 경우 메시지 콘텐츠를 올바르게 인코딩해야 합니다.

```csharp
internal static void PostMessageToQueue(string queueUriWithSas, string message)
{
    CloudQueue queue = new CloudQueue(new Uri(queueUriWithSas));
    CloudQueueMessage queueMessage = new CloudQueueMessage(message);

    queue.AddMessage(queueMessage, null, null, null, null);
}
```

### <a name="check-for-error-messages-from-the-azure-queue"></a>Azure 큐에서 오류 메시지를 확인 합니다.

수집 후에는 데이터 관리에서 기록 하는 관련 큐에서 오류 메시지를 확인 합니다. 실패 메시지 구조에 대 한 자세한 내용은 수집 [실패 메시지 구조](#ingestion-failure-message-structure)를 참조 하세요. 

```csharp
internal static IEnumerable<string> PopTopMessagesFromQueue(string queueUriWithSas, int count)
{
    List<string> messages = Enumerable.Empty<string>().ToList();
    CloudQueue queue = new CloudQueue(new Uri(queueUriWithSas));
    var messagesFromQueue = queue.GetMessages(count);
    foreach (var m in messagesFromQueue)
    {
        messages.Add(m.AsString);
        queue.DeleteMessage(m);
    }

    return messages;
}
```

## <a name="ingestion-messages---json-document-formats"></a>수집 메시지-JSON 문서 형식

### <a name="ingestion-message-internal-structure"></a>수집 메시지 내부 구조

Kusto 데이터 관리 서비스에서 입력 Azure 큐를 읽을 것으로 예상 하는 메시지는 다음 형식의 JSON 문서입니다.

```JSON
{
    "Id" : "<Id>",
    "BlobPath" : "https://<AccountName>.blob.core.windows.net/<ContainerName>/<PathToBlob>?<SasToken>",
    "RawDataSize" : "<RawDataSizeInBytes>",
    "DatabaseName": "<DatabaseName>",
    "TableName" : "<TableName>",
    "RetainBlobOnSuccess" : "<RetainBlobOnSuccess>",
    "FlushImmediately": "<true|false>",
    "ReportLevel" : <0-Failures, 1-None, 2-All>,
    "ReportMethod" : <0-Queue, 1-Table>,
    "AdditionalProperties" : { "<PropertyName>" : "<PropertyValue>" }
}
```

|속성 | 설명 |
|---------|-------------|
|Id |메시지 식별자 (GUID) |
|BlobPath |Blob에 대 한 경로 (URI)입니다. 여기에는 읽기/쓰기/삭제에 대 한 Azure 데이터 탐색기 권한을 부여 하는 SAS 키가 포함 됩니다. Azure 데이터 탐색기가 데이터 수집 완료 되 면 blob을 삭제할 수 있도록 사용 권한이 필요 합니다.|
|RawDataSize |압축 되지 않은 데이터의 크기 (바이트)입니다. 이 값을 제공 하면 Azure 데이터 탐색기는 잠재적으로 여러 blob을 집계 하 여 수집을 최적화할 수 있습니다. 이 속성은 선택 사항 이지만 지정 하지 않는 경우 Azure 데이터 탐색기는 blob에 액세스 하 여 크기를 검색 합니다. |
|DatabaseName |대상 데이터베이스 이름 |
|TableName |대상 테이블 이름 |
|RetainBlobOnSuccess |로 설정 된 경우 수집이 `true` 성공적으로 완료 되 면 blob이 삭제 되지 않습니다. 기본값은 `false` |
|즉시 flush |로 설정 하면 `true` 모든 집계가 생략 됩니다. 기본값은 `false` |
|ReportLevel |성공/오류 보고 수준: 0-실패, 1-없음, 2-모두 |
|ReportMethod |보고 메커니즘: 0-큐, 1-테이블 |
|AdditionalProperties |`format`,, 등의 추가 `tags` 속성 `creationTime` 자세한 내용은 [데이터 수집 속성](../../../ingestion-properties.md)을 참조 하세요.|

### <a name="ingestion-failure-message-structure"></a>수집 실패 메시지 구조

입력 Azure 큐에서 읽을 것으로 예상 되는 데이터 관리 메시지는 다음 형식의 JSON 문서입니다.

|속성 | 설명 |
|---------|-------------
|OperationId |서비스 쪽에서 작업을 추적 하는 데 사용할 수 있는 작업 식별자 (GUID)입니다. |
|데이터베이스 |대상 데이터베이스 이름 |
|테이블 |대상 테이블 이름 |
|FailedOn |오류 타임 스탬프 |
|IngestionSourceId |Azure 데이터 탐색기 수집에 실패 한 데이터 청크를 식별 하는 GUID |
|IngestionSourcePath |Azure 데이터 탐색기 수집에 실패 한 데이터 청크의 경로 (URI) |
|세부 정보 |오류 메시지 |
|오류 코드 |Azure 데이터 탐색기 오류 코드 ( [여기](kusto-ingest-client-errors.md#ingestion-error-codes)의 모든 오류 코드 참조) |
|FailureStatus |오류가 영구적 또는 일시적인 지 여부를 나타냅니다. |
|RootActivityId |서비스 쪽에서 작업을 추적 하는 데 사용할 수 있는 Azure 데이터 탐색기 상관 관계 식별자 (GUID) |
|OriginatesFromUpdatePolicy |오류가 발생 한 [트랜잭션 업데이트 정책](../../management/updatepolicy.md) 으로 인해 오류가 발생 했는지 여부를 나타냅니다. |
|ShouldRetry | 로 다시 시도 하는 경우 수집이 성공할 수 있는지 여부를 나타냅니다. |