---
title: 방법 라이브러리를 사용 하지 않고 데이터 수집을 수집 합니다.-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 Kusto. 수집 라이브러리 없이 방법 데이터 수집을 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.custom: has-adal-ref
ms.date: 02/19/2020
ms.openlocfilehash: 2ea7fd33a6e6ed8728fb12d53fbe76eadf8fd6b6
ms.sourcegitcommit: f6cf88be736aa1e23ca046304a02dee204546b6e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2020
ms.locfileid: "82862074"
---
# <a name="howto-data-ingestion-without-kustoingest-library"></a>방법 라이브러리를 사용 하지 않고 데이터 수집을 수집 합니다.

## <a name="when-to-consider-not-using-kustoingest-library"></a>Kusto. 수집 라이브러리를 사용 하지 않는 것이 좋습니다.
일반적으로 kusto. 수집 라이브러리를 사용 하 여 수집 데이터를 Kusto로 간주할 때마다 선호 해야 합니다.<BR>
이 옵션이 아닌 경우 (일반적으로 OS 제약 조건으로 인해) 거의 동일한 기능을 수행할 수 있는 몇 가지 노력이 필요 합니다.<BR>
이 문서에서는 Kusto. 수집 패키지에 종속성을 적용 하지 않고 Kusto에 **대기 중인** 수집을 구현 하는 방법을 보여 줍니다.

>**참고:** 아래 코드는 샘플 코드를 간소화 하기 위해 Azure Storage SDK, ADAL 인증 라이브러리 및 Newtonsoft.json 패키지를 사용 하 여 c #으로 작성 되었습니다.<BR>필요한 경우 해당 코드를 적절 한 [Azure Storage REST API](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api) 호출, [non-.NET ADAL 패키지](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries) 및 사용 가능한 모든 JSON 처리 패키지로 바꿀 수 있습니다.

## <a name="overview"></a>개요
다음 코드 샘플에서는 Kusto. 수집 라이브러리를 사용 하지 않고, kusto로의 지연 (Kusto 데이터 관리 서비스로 이동)을 보여 줍니다.<BR>
이는 환경 또는 기타 제한으로 인해 전체 .NET을 액세스할 수 없거나 사용할 수 없는 경우에 유용할 수 있습니다.<BR>

> 이 문서에서는 **대기 중인** 수집이 라고도 하는 프로덕션 등급 파이프라인에 대 한 수집의 권장 된 모드를 다룹니다 (Kusto. 수집 라이브러리에서 해당 엔터티는 [IKustoQueuedIngestClient](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient) 인터페이스). 이 모드에서 클라이언트 코드는 Azure 큐에 수집 알림 메시지를 게시 하 여 Kusto 서비스와 상호 작용 합니다. 여기서는 Kusto 데이터 관리 (즉, 수집) 서비스. 데이터 관리 서비스와의 상호 작용은 **AAD**로 인증 되어야 합니다.

이 흐름의 개요는 아래 코드 샘플에 설명 되어 있으며 다음과 같이 구성 됩니다.
1. Azure Storage 클라이언트 만들기 및 blob에 데이터 업로드
1. Kusto 수집 서비스에 액세스 하기 위한 인증 토큰 가져오기
2. 다음을 얻기 위해 Kusto 수집 서비스를 쿼리 합니다.
    * 수집 리소스 (큐 및 blob 컨테이너)
    * 모든 수집 메시지에 추가 되는 kusto id 토큰
3. Kusto in (2)에서 가져온 blob 컨테이너 중 하나의 blob에 데이터 업로드
4. 대상 DB와 테이블을 식별 하 고에서 blob을 가리키는 수집 메시지를 작성 합니다 (3).
5. (4)에 구성 된 수집 메시지를 Kusto in (2)에서 가져온 수집 큐 중 하나에 게시 합니다.
6. 수집 중에 서비스에서 발생 한 오류를 검색 합니다.

이후 섹션에서는 각 단계에 대해 자세히 설명 합니다.

```csharp
// A container class for ingestion resources we are going to obtain from Kusto
internal class IngestionResourcesSnapshot
{
    public IList<string> IngestionQueues { get; set; } = new List<string>();
    public IList<string> TempStorageContainers { get; set; } = new List<string>();

    public string FailureNotificationsQueue { get; set; } = string.Empty;
    public string SuccessNotificationsQueue { get; set; } = string.Empty;
}

public static void IngestSingleFile(string file, string db, string table, string ingestionMappingRef)
{
    // Your Kusto ingestion service URI, typically ingest-<your cluster name>.kusto.windows.net
    string DmServiceBaseUri = @"https://ingest-{serviceNameAndRegion}.kusto.windows.net";

    // 1. Authenticate the interactive user (or application) to access Kusto ingestion service
    string bearerToken = AuthenticateInteractiveUser(DmServiceBaseUri);

    // 2a. Retrieve ingestion resources
    IngestionResourcesSnapshot ingestionResources = RetrieveIngestionResources(DmServiceBaseUri, bearerToken);

    // 2b. Retrieve Kusto identity token
    string identityToken = RetrieveKustoIdentityToken(DmServiceBaseUri, bearerToken);

    // 3. Upload file to one of the blob containers we got from Kusto.
    // This example uses the first one, but when working with multiple blobs,
    // one should round-robin the containers in order to prevent throttling
    long blobSizeBytes = 0;
    string blobName = $"TestData{DateTime.UtcNow.ToString("yyyy-MM-dd_HH-mm-ss.FFF")}";
    string blobUriWithSas = UploadFileToBlobContainer(file, ingestionResources.TempStorageContainers.First(),
                                                            "temp001", blobName, out blobSizeBytes);

    // 4. Compose ingestion command
    string ingestionMessage = PrepareIngestionMessage(db, table, blobUriWithSas, blobSizeBytes, ingestionMappingRef, identityToken);

    // 5. Post ingestion command to one of the ingestion queues we got from Kusto.
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

## <a name="1-obtain-authentication-evidence-from-aad"></a>1. AAD에서 인증 증명 정보 가져오기
여기서는 ADAL을 사용 하 여 입력 큐를 요청 하기 위해 Kusto 데이터 관리 서비스에 액세스 하는 AAD 토큰을 가져옵니다.
ADAL은 필요한 경우 [비 Windows 플랫폼](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries) 에서 사용할 수 있습니다.
```csharp
// Authenticates the interactive user and retrieves AAD Access token for specified resource
internal static string AuthenticateInteractiveUser(string resource)
{
    // Create Auth Context for MSFT AAD:
    AuthenticationContext authContext = new AuthenticationContext("https://login.microsoftonline.com/{AAD Tenant ID or name}");

    // Acquire user token for the interactive user for Kusto:
    AuthenticationResult result =
        authContext.AcquireTokenAsync(resource, "<your client app ID>", new Uri(@"<your client app URI>"),
                                        new PlatformParameters(PromptBehavior.Auto), UserIdentifier.AnyUser, "prompt=select_account").Result;
    return result.AccessToken;
}
```

## <a name="2-retrieve-kusto-ingestion-resources"></a>2. Kusto 수집 리소스를 검색 합니다.
여기서는 흥미로운 작업을 수행 합니다. 여기서는 수집 리소스를 반환 하도록 요청 하는 Kusto 데이터 관리 서비스에 대 한 HTTP POST 요청을 수동으로 생성 합니다.
여기에는 DM 서비스가 수신 대기 하는 큐 및 데이터 업로드를 위한 blob 컨테이너가 포함 됩니다.
데이터 관리 서비스는 이러한 큐 중 하나에 도착 하는 수집 요청을 포함 하는 모든 메시지를 처리 합니다.
```csharp
// Retrieve ingestion resources (queues and blob containers) with SAS from specified Kusto Ingestion service using supplied Access token
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

## <a name="obtaining-kusto-identity-token"></a>Kusto Id 토큰 가져오기
데이터 수집에 대 한 권한을 부여 하는 중요 한 단계는 id 토큰을 가져오고 모든 수집 메시지에 연결 하는 것입니다. 수집 메시지는 비 직접적인 채널 (Azure 큐)을 통해 Kusto에 전달 되므로 대역내 권한 부여 유효성 검사를 수행할 방법이 없습니다. Id 토큰 메커니즘에서는 수집 메시지를 받은 후 Kusto 서비스에서 유효성을 검사할 수 있는 Kusto 서명 id 증명을 발급 하 여이를 허용 합니다.
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

## <a name="3-upload-data-to-azure-blob-container"></a>3. Azure Blob 컨테이너에 데이터 업로드
이 단계에서는 나중에 수집을 위해 전달 되는 Azure Blob에 로컬 파일을 업로드 하는 방법에 대해 알아봅니다. 이 코드는 Azure Storage SDK를 사용 하지만이 종속성을 사용할 수 없는 경우 [Azure Blob Service REST API](https://docs.microsoft.com/rest/api/storageservices/fileservices/blob-service-rest-api)를 사용 하 여 동일 하 게 달성할 수 있습니다.
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

## <a name="4-compose-kusto-ingestion-message"></a>4. Kusto 수집 메시지 작성
여기서 Newtonsoft.json 패키지를 다시 사용 하 여 Azure 큐에 게시 되는 올바른 Kusto 데이터 관리 서비스에서 수신 대기 하는 올바른 수집 요청 메시지를 작성 합니다.
* 수집 메시지의 최소 최소값입니다.
* 해당 id 토큰은 필수 이며 `AdditionalProperties` JSON 개체에 상주해 야 합니다.
* 필요할 때마다 `CsvMapping` 또는 `JsonMapping` 속성도 제공 해야 합니다.
* 자세한 내용은 수집 [매핑 사전 생성 문서를](../../management/create-ingestion-mapping-command.md) 참조 하세요.
* [부록 a](#appendix-a-ingestion-message-internal-structure) 는 수집 메시지 구조에 대 한 설명을 제공 합니다.

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
    message.Add("Format", "json");              // Data is in JSON format
    message.Add("FlushImmediately", true);      // Do not aggregate
    message.Add("ReportLevel", 2);              // Report failures and successes (might incur perf overhead)
    message.Add("ReportMethod", 0);             // Failures are reported to an Azure Queue

    message.Add("AdditionalProperties", new JObject(
                                            new JProperty("authorizationContext", identityToken),
                                            new JProperty("jsonMappingReference", mappingRef)));
    return message.ToString();
}
```

## <a name="5-post-kusto-ingestion-message-to-kusto-ingestion-queue"></a>5. kusto 수집 큐에 Kusto 수집 메시지 게시
마지막으로, deed 자체는 자신이 선택한 큐에 생성 한 메시지를 게시 합니다.<BR>
참고: .Net 저장소 클라이언트를 사용 하는 경우 기본적으로 base64로 메시지를 인코딩합니다. [저장소 문서](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueue.encodemessage)를 참조 하세요.<BR>
해당 클라이언트를 사용 하지 않는 경우 메시지 콘텐츠를 제대로 인코딩해야 합니다.

```csharp
internal static void PostMessageToQueue(string queueUriWithSas, string message)
{
    CloudQueue queue = new CloudQueue(new Uri(queueUriWithSas));
    CloudQueueMessage queueMessage = new CloudQueueMessage(message);

    queue.AddMessage(queueMessage, null, null, null, null);
}
```

## <a name="6-pop-messages-from-an-azure-queue"></a>6. Azure 큐에서 메시지를 팝 합니다.
사후 수집에서이 메서드를 사용 하 여 Kusto 데이터 관리 service에 의해 작성 된 적절 한 큐에서 오류 메시지를 읽습니다.<BR>
[부록 B](#appendix-b-ingestion-failure-message-structure) 는 오류 메시지 구조에 대 한 설명을 제공 합니다.

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

## <a name="appendix-a-ingestion-message-internal-structure"></a>부록 A: 메시지 내부 구조 수집
Kusto 데이터 관리 서비스에서 입력 Azure 큐를 읽을 것으로 예상 하는 메시지는 다음과 같은 형식의 JSON 문서입니다.

```json
{
    "Id" : "<Id>",
    "BlobPath" : "https://<AccountName>.blob.core.windows.net/<ContainerName>/<PathToBlob>?<SasToken>",
    "RawDataSize" : "<RawDataSizeInBytes>",
    "DatabaseName": "<DatabaseName>",
    "TableName" : "<TableName>",
    "RetainBlobOnSuccess" : "<RetainBlobOnSuccess>",
    "Format" : "<csv|tsv|...>",
    "FlushImmediately": "<true|false>",
    "ReportLevel" : <0-Failures, 1-None, 2-All>,
    "ReportMethod" : <0-Queue, 1-Table>,
    "AdditionalProperties" : { "<PropertyName>" : "<PropertyValue>" }
}
```


|속성 | Description |
|---------|-------------|
|Id |메시지 식별자 (GUID) |
|BlobPath |읽기/쓰기/삭제 권한을 부여 하는 SAS 키를 포함 하는 blob URI (Kusto가 데이터 수집 완료 되 면 blob을 삭제 하는 경우 쓰기/삭제 권한이 필요 함) |
|RawDataSize |압축 되지 않은 데이터의 크기 (바이트)입니다. 이 값을 제공 하면 Kusto에서 여러 blob을 함께 집계할 수 있으므로 수집을 최적화할 수 있습니다. 이 속성은 선택 사항 이지만 제공 되지 않은 경우 Kusto는 blob에 액세스 하 여 크기를 검색 합니다. |
|DatabaseName |대상 데이터베이스 이름 |
|TableName |대상 테이블 이름 |
|RetainBlobOnSuccess |로 `true`설정 된 경우 수집이 성공적으로 완료 되 면 blob이 삭제 되지 않습니다. 기본값은 `false`입니다. |
|형식 |압축 되지 않은 데이터 형식 |
|즉시 flush |로 `true`설정 하면 모든 집계가 생략 됩니다. 기본값은 `false`입니다. |
|ReportLevel |성공/오류 보고 수준: 0-실패, 1-없음, 2-모두 |
|ReportMethod |보고 메커니즘: 0-큐, 1-테이블 |
|AdditionalProperties |추가 속성 (태그 등) |


## <a name="appendix-b-ingestion-failure-message-structure"></a>부록 B: 수집 실패 메시지 구조
Kusto 데이터 관리 service에서 입력 Azure 큐를 읽을 것으로 예상 하는 다음 테이블 메시지는 다음과 같은 형식의 JSON 문서입니다.

|속성 | Description |
|---------|-------------
|OperationId |서비스 쪽에서 작업을 추적 하는 데 사용할 수 있는 작업 식별자 (GUID)입니다. |
|데이터베이스 |대상 데이터베이스 이름 |
|테이블 |대상 테이블 이름 |
|FailedOn |오류 타임 스탬프 |
|IngestionSourceId |Kusto 수집에 실패 한 데이터 청크를 식별 하는 GUID |
|IngestionSourcePath |Kusto 수집에 실패 한 데이터 청크의 경로 (URI) |
|세부 정보 |오류 메시지 |
|오류 코드 |Kusto 오류 코드 ( [여기](kusto-ingest-client-errors.md#ingestion-error-codes)에서 모든 오류 코드 참조) |
|FailureStatus |오류가 영구적 또는 일시적인 지 여부를 나타냅니다. |
|RootActivityId |서비스 측에서 작업을 추적 하는 데 사용할 수 있는 kusto 상관 관계 식별자 (GUID)입니다. |
|OriginatesFromUpdatePolicy |오류가 errorneous [트랜잭션 업데이트 정책](../../management/updatepolicy.md) 으로 인해 발생 했는지 여부를 나타냅니다. |
|ShouldRetry | 로 다시 시도 하는 경우 수집이 성공할 수 있는지 여부를 나타냅니다. |