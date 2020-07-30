---
title: Kusto. 수집 오류 & 예외-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 Kusto 수집 오류 및 예외에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/30/2019
ms.openlocfilehash: 6b94dfc0fab1150b598fad9d55beec2f3a81ad73
ms.sourcegitcommit: f34535b0ca63cff22e65c598701cec13856c1742
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/29/2020
ms.locfileid: "87402341"
---
# <a name="kustoingest-errors-and-exceptions"></a>Kusto. 수집 오류 및 예외
클라이언트 쪽에서 수집을 처리 하는 동안 발생 하는 모든 오류는 c # 예외로 표시 됩니다.

## <a name="failures"></a>오류

### <a name="kustodirectingestclient-exceptions"></a>KustoDirectIngestClient 예외

여러 소스에서 수집을 시도 하는 동안 수집 프로세스 중에 오류가 발생할 수 있습니다. 원본 중 하나에 대해 수집에 실패 하는 경우 기록 되 고 클라이언트는 계속 해 서 나머지 원본을 수집 합니다. 모든 원본에서 수집을 수행한 후에는 `IngestClientAggregateException` 멤버가 포함 된이 throw 됩니다 `IList<IngestClientException> IngestionErrors` .

`IngestClientException`및 해당 파생 클래스에는 필드 `IngestionSource` 와 `Error` 필드가 포함 됩니다. 두 필드는 수집에 실패 한 원본에서 수집을 시도 하는 동안 발생 한 오류에 대 한 매핑을 만듭니다. 이 정보를 목록에서 사용 하 여 수집에 `IngestionErrors` 실패 한 원본 및 이유를 조사할 수 있습니다. `IngestClientAggregateException`또한 예외에는 `GlobalError` 모든 원본에 대 한 오류가 발생 했는지 여부를 나타내는 부울 속성도 포함 되어 있습니다.

### <a name="failures-ingesting-from-files-or-blobs"></a>파일이 나 blob에서 수집 오류

Blob 또는 파일에서 수집을 시도 하는 동안 수집 오류가 발생 한 경우 `deleteSourceOnSuccess` 에는 플래그가로 설정 된 경우에도 수집 원본이 삭제 되지 않습니다 `true` . 원본은 추가 분석을 위해 유지 됩니다. 오류의 출처를 이해 하 고 수집 소스 자체에서 오류가 발생 하지 않은 경우 클라이언트의 사용자는 다시 수집 하려고 시도할 수 있습니다.

### <a name="failures-ingesting-from-idatareader"></a>IDataReader의 수집 오류

DataReader에서 수집 하는 동안 수집 되는 데이터는 기본 위치가 인 임시 폴더에 저장 됩니다 `<Temp Path>\Ingestions_<current date and time>` . 이 기본 폴더는 성공적으로 수집 된 후 항상 삭제 됩니다.

`IngestFromDataReader`및 `IngestFromDataReaderAsync` 메서드에서 기본값이 인 플래그는 `retainCsvOnFailure` 실패 한 수집 후에 파일을 `false` 유지할지 여부를 결정 합니다. 이 플래그가로 설정 되 면 `false` 수집에 실패 한 데이터는 지속 되지 않으므로 무엇이 잘못 되었는지 이해 하기가 어렵습니다.

## <a name="kustoqueuedingestclient-exceptions"></a>KustoQueuedIngestClient 예외

`KustoQueuedIngestClient`Azure 큐에 메시지를 업로드 하 여 데이터를 수집 합니다. 큐 프로세스 도중 이나 도중에 오류가 발생 하는 경우 `IngestClientAggregateException` 프로세스가 종료 될 때이 throw 됩니다. Throw 된 예외에는 `IngestClientException` 각 오류의 소스가 포함 되 고 큐에 게시 되지 않은의 컬렉션이 포함 됩니다. 메시지 게시를 시도 하는 동안 발생 한 오류도 throw 됩니다.

### <a name="posting-to-queue-failures-with-a-file-or-blob-as-a-source"></a>파일이 나 blob을 원본으로 사용 하 여 큐 오류에 게시

의 메서드를 사용 하는 동안 오류가 발생 하는 경우 `KustoQueuedIngestClient` `IngestFromFile/IngestFromBlob` 플래그가로 설정 된 경우에도 소스는 삭제 되지 않습니다 `deleteSourceOnSuccess` `true` . 대신, 추가 분석을 위해 원본이 유지 됩니다. 

오류의 출처를 이해 하 고 수집 소스 자체에서 오류가 발생 하지 않은 경우 클라이언트 사용자는 실패 한 소스와 관련 된 메서드를 사용 하 여 데이터를 다시 대기 할 수 있습니다 `IngestFromFile/IngestFromBlob` . 

### <a name="posting-to-queue-failures-with-idatareader-as-a-source"></a>IDataReader를 원본으로 사용 하 여 큐 오류에 게시

DataReader 원본을 사용 하는 동안 큐에 게시할 데이터는 기본 위치가 인 임시 폴더에 저장 됩니다 `<Temp Path>\Ingestions_<current date and time>` . 이 폴더는 데이터가 큐에 성공적으로 게시 된 후 항상 삭제 됩니다.
`IngestFromDataReader`및 `IngestFromDataReaderAsync` 메서드에서 기본값이 인 플래그는 `retainCsvOnFailure` 실패 한 수집 후에 파일을 `false` 유지할지 여부를 결정 합니다. 이 플래그가로 설정 되 면 `false` 수집에 실패 한 데이터는 지속 되지 않으므로 무엇이 잘못 되었는지 이해 하기가 어렵습니다.

### <a name="common-failures"></a>일반적인 오류
|Error                         |이유           |완화 방법                                   |
|------------------------------|-----------------|---------------------------------------------|
|데이터베이스 <database name> 이름이 없습니다.| 데이터베이스가 존재 하지 않습니다.|데이터베이스 이름 (데이터베이스 이름)을 확인 합니다. `kustoIngestionProperties` |
|' Table ' 유형의 엔터티 ' 테이블 이름이 없습니다. '를 찾을 수 없습니다.|테이블이 존재 하지 않으며 CSV 매핑이 없습니다.| CSV 매핑 추가/필수 테이블 만들기 |
|<blob path>이유로 Blob 제외: JSON 패턴은 jsonMapping 매개 변수를 사용 하 여 수집 여야 합니다.| Json 매핑이 제공 되지 않는 경우 JSON 수집.|JSON 매핑 제공 |
|Blob을 다운로드 하지 못했습니다. ' 원격 서버에서 오류를 반환 했습니다. (404)를 찾을 수 없습니다. '| Blob이 존재하지 않습니다.|Blob이 있는지 확인 합니다. 있는 경우 다시 시도 하 고 Kusto 팀에 문의 하세요. |
|JSON 열 매핑이 잘못 되었습니다. 둘 이상의 매핑 요소가 동일한 열을 가리킵니다.| JSON 매핑에 다른 경로를 사용 하는 2 개의 열이 있습니다.|JSON 매핑 수정 |
|EngineError-[UtilsException] `IngestionDownloader.Download` : 하나 이상의 파일을 다운로드 하지 못했습니다 (작업 id: <GUID1> , rootactivityid id: <GUID2> ).| 하나 이상의 파일을 다운로드 하지 못했습니다. |다시 시도 |
|구문 분석 하지 못했습니다. ID가 ' <stream name> ' 인 스트림에 잘못 된 형식의 CSV 형식이 있습니다 .이는 ValidationOptions 정책에 따라 실패 합니다. |CSV 파일의 형식이 잘못 되었습니다 (예: 모든 줄에 동일한 수의 열을 포함 하지 않음). 유효성 검사 정책이로 설정 된 경우에만 실패 `ValidationOptions` 합니다. ValidateCsvInputConstantColumns |CSV 파일을 확인 합니다. 이 메시지는 CSV/TSV 파일에만 적용 됩니다. |
|`IngestClientAggregateException`"올바른 공유 액세스 서명에 대 한 필수 매개 변수가 없습니다." 라는 오류 메시지가 |사용 중인 SAS는 저장소 계정이 아닌 서비스의 서비스입니다. |저장소 계정의 SAS 사용 |

### <a name="ingestion-error-codes"></a>수집 오류 코드

수집 오류를 프로그래밍 방식으로 처리 하는 데 도움이 되도록 오류 정보는 숫자 오류 코드 ()와 보강 됩니다 `IngestionErrorCode enumeration` .

|오류 코드                                      |이유                                                        |
|-----------------------------------------------|--------------------------------------------------------------|
|알 수 없음                                        | 알 수 없는 오류 발생|
|Stream_LowMemoryCondition                      | 작업에 메모리가 부족 합니다.|
|Stream_WrongNumberOfFields                     | CSV 문서의 필드 수가 일치 하지 않습니다.|
|Stream_InputStreamTooLarge                     | 수집을 위해 전송 된 문서가 허용 된 크기를 초과 했습니다.|
|Stream_NoDataToIngest                          | 수집할 데이터 스트림이 없습니다.|
|Stream_DynamicPropertyBagTooLarge              | 수집 데이터의 동적 열 중 하나에 고유한 속성이 너무 많습니다.|
|Download_SourceNotFound                        | Azure storage에서 소스를 다운로드 하지 못했습니다. 소스를 찾을 수 없습니다.|
|Download_AccessConditionNotSatisfied           | Azure storage에서 소스를 다운로드 하지 못했습니다.-액세스가 거부 되었습니다.|
|Download_Forbidden                             | Azure storage에서 소스를 다운로드 하지 못했습니다. 액세스가 허용 되지 않습니다.|
|Download_AccountNotFound                       | Azure storage에서 소스를 다운로드 하지 못했습니다. 계정을 찾을 수 없습니다.|
|Download_BadRequest                            | Azure storage에서 소스를 다운로드 하지 못했습니다. 잘못 된 요청|
|Download_NotTransient                          | Azure storage에서 소스를 다운로드 하지 못했습니다. 일시적인 오류가 아닙니다.|
|Download_UnknownError                          | Azure storage에서 소스를 다운로드 하지 못했습니다. 알 수 없는 오류|
|UpdatePolicy_QuerySchemaDoesNotMatchTableSchema| 업데이트 정책을 호출 하지 못했습니다. 쿼리 스키마가 테이블 스키마와 일치 하지 않습니다.|
|UpdatePolicy_FailedDescendantTransaction       | 업데이트 정책을 호출 하지 못했습니다. 실패 한 하위 트랜잭션 업데이트 정책|
|UpdatePolicy_IngestionError                    | 업데이트 정책을 호출 하지 못했습니다. 수집 오류가 발생 했습니다.|
|UpdatePolicy_UnknownError                      | 업데이트 정책을 호출 하지 못했습니다. 알 수 없는 오류 발생|
|BadRequest_MissingJsonMappingtFailure          | JSON 패턴이 jsonMapping 매개 변수를 사용 하 여 수집 되지 않았습니다.|
|BadRequest_InvalidBlob                         | 엔진이 비 zip blob을 열고 읽지 못했습니다.|
|BadRequest_EmptyBlob                           | 빈 blob|
|BadRequest_EmptyArchive                        | Zip 파일에 보관 된 요소가 없습니다.|
|BadRequest_EmptyBlobUri                        | 지정 된 blob URI가 비어 있습니다.|
|BadRequest_DatabaseNotExist                    | 데이터베이스가 존재 하지 않습니다.|
|BadRequest_TableNotExist                       | 테이블이 없습니다.|
|BadRequest_InvalidKustoIdentityToken           | 잘못 된 Kusto id 토큰|
|BadRequest_UriMissingSas                       | 알 수 없는 blob storage의 SAS가 없는 blob 경로|
|BadRequest_FileTooLarge                        | 너무 많은 파일을 수집 하려고 합니다.|
|BadRequest_NoValidResponseFromEngine           | 수집 명령의 유효한 회신이 없습니다.|
|BadRequest_TableAccessDenied                   | 테이블에 대 한 액세스가 거부 되었습니다.|
|BadRequest_MessageExhausted                    | 메시지가 모두 사용 됨|
|General_BadRequest                             | 일반적인 잘못 된 요청입니다. 존재 하지 않는 데이터베이스/테이블에 대 한 수집 시 힌트를 적용할 수 있습니다.|
|General_InternalServerError                    | 내부 서버 오류가 발생 했습니다.|

## <a name="detailed-exceptions-reference"></a>자세한 예외 참조

### <a name="cloudqueuesnotfoundexception"></a>CloudQueuesNotFoundException

데이터 관리 클러스터에서 큐가 반환 되지 않은 경우 발생 합니다.

기본 클래스: [예외](https://msdn.microsoft.com/library/system.exception(v=vs.110).aspx)

|필드 이름 |형식     |의미
|-----------|---------|------------------------------|
|Error      | 문자열  | DM에서 큐를 검색 하는 동안 발생 한 오류입니다.
                            
[Kusto 대기 중인 수집 클라이언트](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient)를 사용 하는 경우에만 해당 됩니다.
수집 프로세스 중에는 DM에 연결 된 Azure 큐를 검색 하기 위해 몇 번의 시도가 수행 됩니다. 이러한 시도가 실패할 경우 실패 이유를 포함 하는 예외는 ' 오류 ' 필드에서 발생 합니다. ' InnerException ' 필드의 내부 예외도 발생할 수 있습니다.


### <a name="cloudblobcontainersnotfoundexception"></a>CloudBlobContainersNotFoundException

데이터 관리 클러스터에서 blob 컨테이너가 반환 되지 않은 경우 발생 합니다.

기본 클래스: [예외](https://msdn.microsoft.com/library/system.exception(v=vs.110).aspx)

|필드 이름   |형식     |의미       
|-------------|---------|------------------------------|
|KustoEndpoint| 문자열  | 관련 DM의 끝점입니다.
                            
[Kusto 대기 중인 수집 클라이언트](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient)를 사용 하는 경우에만 해당 됩니다.  
수집가 아직 Azure 컨테이너에 없는 소스 (예: 파일, DataReader 또는 스트림)를 사용할 경우 수집을 위해 임시 blob에 데이터를 업로드 합니다. 데이터를 업로드할 컨테이너가 없는 경우 예외가 발생 합니다.

### <a name="duplicateingestionpropertyexception"></a>DuplicateIngestionPropertyException

수집 속성이 두 번 이상 구성 될 때 발생 합니다.

기본 클래스: [예외](https://msdn.microsoft.com/library/system.exception(v=vs.110).aspx)

|필드 이름   |형식     |의미       
|-------------|---------|------------------------------------|
|PropertyName | 문자열  | 중복 속성의 이름입니다.
                            
### <a name="postmessagetoqueuefailedexception"></a>PostMessageToQueueFailedException

큐에 메시지를 게시 하지 못할 때 발생 합니다.

기본 클래스: [예외](https://msdn.microsoft.com/library/system.exception(v=vs.110).aspx)

|필드 이름   |형식     |의미       
|-------------|---------|---------------------------------|
|QueueUri     | 문자열  | 큐의 URI입니다.
|Error        | 문자열  | 큐에 게시 하는 동안 생성 된 오류 메시지입니다.
                            
[Kusto 대기 중인 수집 클라이언트](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient)를 사용 하는 경우에만 해당 됩니다.  
대기 중인 수집 클라이언트는 관련 Azure 큐에 메시지를 업로드 하 여 데이터를 수집 합니다. Post 오류가 발생 하면 예외가 발생 합니다. 여기에는 큐 URI, ' 오류 ' 필드의 실패 이유 및 ' InnerException ' 필드의 내부 예외가 포함 됩니다.

### <a name="dataformatnotspecifiedexception"></a>DataFormatNotSpecifiedException

데이터 형식이 필요 하지만에 지정 되지 않은 경우 발생 합니다.`IngestionProperties`

기본 클래스: IngestClientException

스트림에서 수집 때 데이터를 제대로 수집 하려면 [IngestionProperties](kusto-ingest-client-reference.md#class-kustoingestionproperties)에서 데이터 형식을 지정 해야 합니다. 이 예외는가 `IngestionProperties.Format` 지정 되지 않은 경우에 발생 합니다.

### <a name="invaliduriingestclientexception"></a>InvalidUriIngestClientException

잘못 된 blob URI가 수집 소스로 제출 될 때 발생 합니다.

기본 클래스: IngestClientException

### <a name="compressfileingestclientexception"></a>CompressFileIngestClientException

수집 클라이언트가 수집을 위해 제공 된 파일을 압축 하지 못할 때 발생 합니다.

기본 클래스: IngestClientException

파일은 수집 하기 전에 압축 됩니다. 파일 압축 시도가 실패 하면 예외가 발생 합니다.

### <a name="uploadfiletotempblobingestclientexception"></a>UploadFileToTempBlobIngestClientException

수집 클라이언트가 임시 blob에 수집 하기 위해 제공 된 원본을 업로드 하지 못할 때 발생 합니다.

기본 클래스: IngestClientException

### <a name="sizelimitexceededingestclientexception"></a>SizeLimitExceededIngestClientException

수집 원본이 너무 클 때 발생 합니다.

기본 클래스: IngestClientException

|필드 이름   |형식     |의미       
|-------------|---------|-----------------------|
|크기         | long    | 수집 원본의 크기
|MaxSize      | long    | 수집에 허용 되는 최대 크기

수집 원본이 최대 크기인 4GB를 초과 하는 경우 예외가 throw 됩니다. 크기 유효성 검사는 `IgnoreSizeLimit` [IngestionProperties 클래스](kusto-ingest-client-reference.md#class-kustoingestionproperties)의 플래그로 재정의할 수 있습니다. 그러나 1gb [보다 큰 단일 원본을 수집 하지](about-kusto-ingest.md#ingestion-best-practices)않는 것이 좋습니다.

### <a name="uploadfiletotempblobingestclientexception"></a>UploadFileToTempBlobIngestClientException

수집 클라이언트가 임시 blob에 수집 하기 위해 제공 된 파일을 업로드 하지 못할 때 발생 합니다.

기본 클래스: IngestClientException

### <a name="directingestclientexception"></a>DirectIngestClientException

직접 수집을 수행 하는 동안 일반 오류가 발생할 때 발생 합니다.

기본 클래스: IngestClientException

### <a name="queuedingestclientexception"></a>QueuedIngestClientException

대기 중인 수집을 수행 하는 동안 오류가 발생할 때 발생 합니다.

기본 클래스: IngestClientException

### <a name="ingestclientaggregateexception"></a>IngestClientAggregateException

수집 하는 동안 하나 이상의 오류가 발생할 때 발생 합니다.

기본 클래스: [AggregateException](https://msdn.microsoft.com/library/system.aggregateexception(v=vs.110).aspx)

|필드 이름      |형식                             |의미       
|----------------|---------------------------------|-----------------------|
|IngestionErrors | IList<IngestClientException>    | 수집 하려고 하는 동안 발생 하는 오류 및 해당 오류와 관련 된 소스
|IsGlobalError   | bool                            | 모든 원본에 대 한 예외가 발생 했는지 여부를 나타냅니다.

## <a name="next-steps"></a>다음 단계

네이티브 코드의 오류에 대 한 자세한 내용은 [네이티브 코드의 오류](../../concepts/errorsinnativecode.md)를 참조 하세요.
