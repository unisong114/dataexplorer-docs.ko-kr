---
title: Kusto.Ingest 참조 - 오류 및 예외 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Kusto.Ingest 참조 - Azure 데이터 탐색기의 오류 및 예외에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/30/2019
ms.openlocfilehash: f8f50322a79dea8890b4a4ad5eaa78f0b8fe3bc0
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81524347"
---
# <a name="kustoingest-reference---errors-and-exceptions"></a>Kusto.Ingest 참조 - 오류 및 예외
클라이언트 측에서 의 한 번의 섭취 처리 중에 오류가 C# 예외를 통해 사용자 코드에 노출됩니다.

## <a name="failures-overview"></a>오류 개요

### <a name="kustodirectingestclient-exceptions"></a>쿠스토다이렉트클라이언트 예외
여러 소스에서 수집을 시도하는 동안 일부 소스를 수집하는 동안 오류가 발생할 수 있지만 다른 소스는 성공적으로 수집될 수 있습니다. 특정 원본에 대한 수집에 실패하면 수집이 기록되고 클라이언트가 수집을 위해 나머지 소스를 계속 수집합니다. 수집을 위해 모든 소스를 검색한 `IngestClientAggregateException` 후 멤버를 `IList<IngestClientException> IngestionErrors`포함하는 throw됩니다.
`IngestClientException`파생 클래스에는 소스에서 `IngestionSource` 맵핑을 구성하는 필드와 `Error` 필드를 함께 사용하여 인제하려고 시도하는 동안 발생한 오류로 인제되지 않은 필드가 포함되어 있습니다. InestionErrors 목록의 정보를 사용하여 수집되지 않은 소스와 그 이유를 조사할 수 있습니다. `IngestClientAggregateException`예외에는 모든 소스에 `GlobalError`대해 오류가 발생했는지 여부를 나타내는 부울 속성도 포함되어 있습니다.

### <a name="failures-ingesting-from-files-or-blobs"></a>파일 또는 Blob에서 가져오는 오류 
Blob\file에서 수집을 시도하는 동안 수집 실패가 발생한 경우 `deleteSourceOnSuccess` 플래그가 `true`로 설정되어 있더라도 수집 소스가 삭제되지 않습니다.
추가 분석을 위해 소스가 보존됩니다. 오류의 출처를 이해하고 오류가 인시던트 원본 자체에서 시작되지 않았음을 감안할 때 클라이언트 사용자는 오류의 원인을 다시 인식하려고 시도할 수 있습니다.

### <a name="failures-ingesting-from-idatareader"></a>IDataReader에서 수집한 오류
DataReader에서 수집하는 동안 수집할 데이터는 기본 위치가 있는 임시 `<Temp Path>\Ingestions_<current date and time>`폴더에 저장됩니다. 이 폴더는 성공적으로 받은 후에 항상 삭제됩니다.<BR>
`IngestFromDataReader` 및 `IngestFromDataReaderAsync` 메서드에서 기본값이 기본값인 `retainCsvOnFailure` `false`플래그는 실패한 인베이션 후에 파일을 보관할지 여부를 결정합니다. 이 플래그가 `false`으로 설정된 경우 수집에 실패한 데이터는 유지되지 않아 무엇이 잘못되었는지 이해하기 가 어려워지게 됩니다.

## <a name="kustoqueuedingestclient-exceptions"></a>쿠스토큐잉스트클라이언트 예외
KustoQueuedIngestClient Azure 큐에 메시지를 업로드 하 여 데이터를 수집 합니다. 큐에 대기 프로세스 전과 중에 오류가 `IngestClientAggregateException` 발생하면 큐에 게시되지 않은 소스(모든 오류)와 메시지를 게시하는 동안 발생한 오류가 포함된 컬렉션이 `IngestClientException` 포함된 실행 종료 시 에러가 throw됩니다.

### <a name="posting-to-queue-failures-with-file-or-blob-as-a-source"></a>파일 또는 Blob을 소스로 사용하여 큐 오류에 게시
KustoQueuedIngestClient의 IngestFromFile/IngestFromBlob 메서드를 사용하는 동안 오류가 발생한 경우 `deleteSourceOnSuccess` 플래그가 `true`로 설정되어 있더라도 소스가 삭제되지 않고 추가 분석을 위해 보존됩니다. 오류의 출처를 이해하고 오류가 원본 자체에서 시작되지 않았음을 감안할 때 클라이언트 사용자는 실패한 원본과 관련 IngestFromFile/IngestFromBlob 메서드를 사용하여 데이터를 다시 큐에 대기하려고 시도할 수 있습니다. 

### <a name="posting-to-queue-failures-with-idatareader-as-a-source"></a>소스로 IDataReader와 큐 실패에 게시
DataReader 원본을 사용하는 동안 큐에 게시할 데이터는 기본 위치가 있는 `<Temp Path>\Ingestions_<current date and time>`임시 폴더에 저장됩니다.
이 폴더는 데이터가 큐에 성공적으로 게시된 후에 항상 삭제됩니다.
`IngestFromDataReader` 및 `IngestFromDataReaderAsync` 메서드에서 기본값이 기본값인 `retainCsvOnFailure` `false`플래그는 실패한 인베이션 후에 파일을 보관할지 여부를 결정합니다. 이 플래그가 `false`으로 설정된 경우 수집에 실패한 데이터는 유지되지 않아 무엇이 잘못되었는지 이해하기 가 어려워지게 됩니다.

### <a name="common-failures"></a>일반적인 오류
|Error|이유|완화 방법|
|------------------------------|----|------------|
|데이터베이스 <database name> 이름이 없습니다.| 데이터베이스가 없습니다.|kustoIngestion속성/데이터베이스 만들기에서 데이터베이스 이름 확인 |
|종류 '테이블'의 엔터티 '테이블 이름'을 찾을 수 없습니다.|테이블이 없고 CSV 매핑이 없습니다.| CSV 매핑 추가 / 필요한 테이블 만들기 |
|Blob <blob path> 은 이유로 제외: json 패턴jsonMapping 매개 변수로 인제되어야 합니다.| Json 매핑이 제공되지 않은 경우 Json 섭취.|JSON 매핑 제공 |
|Blob을 다운로드하지 못했습니다: '원격 서버에서 오류를 반환했습니다. (404) 찾을 수 없습니다.'| Blob이 없습니다.|다시 시도할 경우 Blob이 있는지 확인하고 Kusto 팀에 문의하십시오. |
|Json 열 매핑이 잘못되었습니다: 둘 이상의 매핑 요소가 동일한 열을 가리킵니다.| JSON 매핑에는 경로가 다른 2개의 열이 있습니다.|JSON 매핑 수정 |
|EngineError - [UtilsException] 인제션다운로드.다운로드: 하나 이상의 파일을 다운로드하지 못했습니다 (활동ID에<GUID1>대한 KustoLogs 검색: , RootActivityId:<GUID2>)| 하나 이상의 파일을 다운로드하지 못했습니다. |다시 시도 |
|구문 분석실패: id '가 있는 스트림 '<stream name>형식이 잘못된 Csv 형식, 유효성 검사 옵션 정책당 실패 |잘못된 csv 파일(예: 모든 줄에서 동일한 열 수가 아님). 유효성 검사 정책이 유효성 검사 옵션으로 설정된 경우에만 실패합니다. 유효성 검사Csv입력상수열 |CSV 파일을 확인합니다. 이 메시지는 csv/tsv 파일에만 적용됩니다. |
|인제스트클라이언트AggregateException 오류 메시지 '유효한 공유 액세스 서명에 대 한 필수 매개 변수 누락' |사용 중인 SAS는 저장소 계정이 아닌 서비스입니다. |저장소 계정의 SAS 사용 |

### <a name="ingestion-error-codes"></a>인비전에이션 오류 코드

수집 오류를 프로그래밍 방식으로 처리하는 데 도움이 되는 오류 정보는 숫자 오류 코드(IngestionErrorCode 열거)로 보강됩니다.

|ErrorCode|이유|
|-----------|-------|
|알 수 없음| 알 수 없는 오류 발생|
|Stream_LowMemoryCondition| 메모리 부족 작업|
|Stream_WrongNumberOfFields| CSV 문서에 일치하지 않는 필드 수가 있습니다.|
|Stream_InputStreamTooLarge| 허용 된 크기를 초과 했습니다.|
|Stream_NoDataToIngest| 수집할 데이터 스트림을 찾지 못했습니다.|
|Stream_DynamicPropertyBagTooLarge| 수집된 데이터의 동적 열 중 하나에 너무 많은 고유 속성이 포함되어 있습니다.|
|Download_SourceNotFound| Azure 저장소에서 원본을 다운로드하지 못했습니다- 소스를 찾을 수 없습니다.|
|Download_AccessConditionNotSatisfied| Azure 저장소에서 원본을 다운로드하지 못했습니다 - 액세스가 거부됨|
|Download_Forbidden| Azure 저장소에서 원본을 다운로드하지 못했습니다 - 액세스가 금지됨|
|Download_AccountNotFound| Azure 저장소에서 원본을 다운로드하지 못했습니다 - 계정을 찾을 수 없습니다.|
|Download_BadRequest| Azure 저장소에서 원본을 다운로드하지 못했습니다.|
|Download_NotTransient| Azure 저장소에서 원본을 다운로드하지 못했습니다- 일시적인 오류가 아님|
|Download_UnknownError| Azure 저장소에서 원본을 다운로드하지 못했습니다- 알 수 없는 오류|
|UpdatePolicy_QuerySchemaDoesNotMatchTableSchema| 업데이트 정책을 호출하지 못했습니다. 쿼리 스키마가 테이블 스키마와 일치하지 않음|
|UpdatePolicy_FailedDescendantTransaction| 업데이트 정책을 호출하지 못했습니다. 실패한 하위 트랜잭션 업데이트 정책|
|UpdatePolicy_IngestionError| 업데이트 정책을 호출하지 못했습니다. 인스티온 오류가 발생했습니다.|
|UpdatePolicy_UnknownError| 업데이트 정책을 호출하지 못했습니다. 알 수 없는 오류 발생|
|BadRequest_MissingJsonMappingtFailure| json 패턴이 jsonMapping 매개 변수로 섭취되지 않았습니다.|
|BadRequest_InvalidOrEmptyBlob| Blob이 유효하지 않거나 빈 zip 아카이브입니다.|
|BadRequest_DatabaseNotExist| 데이터베이스가 없습니다.|
|BadRequest_TableNotExist| 테이블이 없습니다.|
|BadRequest_InvalidKustoIdentityToken| 잘못된 쿠스토 ID 토큰|
|BadRequest_UriMissingSas| 알 수 없는 Blob 저장소에서 SAS가 없는 Blob 경로|
|BadRequest_FileTooLarge| 너무 큰 파일을 인더킹하려고 합니다.|
|BadRequest_NoValidResponseFromEngine| 인제스트 명령에서 유효한 회신이 없습니다.|
|BadRequest_TableAccessDenied| 테이블에 대한 액세스가 거부되었습니다.|
|BadRequest_MessageExhausted| 메시지가 소진되었습니다.|
|General_BadRequest| 일반 불량 요청(존재하지 않는 데이터베이스/테이블에 대한 인베이션을 암시할 수 있음)|
|General_InternalServerError| 내부 서버 오류가 발생했습니다.|

## <a name="detailed-kustoingest-exceptions-reference"></a>자세한 Kusto.Ingest 예외 참조

### <a name="cloudqueuesnotfoundexception"></a>클라우드큐스NotFound예외
데이터 관리 클러스터에서 큐가 반환되지 않은 경우 발생합니다.

기본 클래스: [예외](https://msdn.microsoft.com/library/system.exception(v=vs.110).aspx)

필드:

|속성|Type|의미
|-----------|----|------------------------------|
|Error| `String`| DM에서 큐를 검색하는 동안 발생한 오류
                            
추가 정보:

[Kusto 큐에 대기된 인제스트 클라이언트를](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient)사용하는 경우에만 관련이 있습니다.
인기 프로세스 중에 DM에 연결된 Azure 큐를 검색하기 위해 여러 번 시도합니다. 이러한 시도가 실패하면 '오류' 필드의 실패 이유와 'InnerException' 필드의 내부 예외가 포함된 예외가 발생합니다.


### <a name="cloudblobcontainersnotfoundexception"></a>클라우드블b컨테이너NotFound예외
데이터 관리 클러스터에서 Blob 컨테이너가 반환되지 않은 경우 발생합니다.

기본 클래스: [예외](https://msdn.microsoft.com/library/system.exception(v=vs.110).aspx)

필드:

|속성|Type|의미       
|-----------|----|------------------------------|
|쿠스토엔드포인트| `String`| 관련 DM의 끝점
                            
추가 정보:

[Kusto 큐에 대기된 인제스트 클라이언트를](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient)사용하는 경우에만 관련이 있습니다.  
Azure 컨테이너에 아직 없는 소스(예: 파일, DataReader 또는 Stream)를 수집할 때 수집을 위해 데이터가 임시 Blob에 업로드됩니다. 데이터를 업로드할 컨테이너가 없는 경우 예외가 발생합니다.

### <a name="duplicateingestionpropertyexception"></a>중복속성예외
인셉션 속성이 두 번 이상 구성될 때 발생합니다.

기본 클래스: [예외](https://msdn.microsoft.com/library/system.exception(v=vs.110).aspx)

필드:

|속성|Type|의미       
|-----------|----|------------------------------|
|PropertyName| `String`| 중복 속성의 이름
                            
### <a name="postmessagetoqueuefailedexception"></a>포스트 메시지ToQueue실패예외
큐에 메시지를 게시하는 데 실패할 때 발생했습니다.

기본 클래스: [예외](https://msdn.microsoft.com/library/system.exception(v=vs.110).aspx)

필드:

|속성|Type|의미       
|-----------|----|------------------------------|
|큐우리| `String`| 큐의 URI
|Error| `String`| 큐에 게시하는 동안 생성된 오류 메시지
                            
추가 정보:

[Kusto 큐에 대기된 인제스트 클라이언트를](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient)사용하는 경우에만 관련이 있습니다.  
큐에 대기된 인제스트 클라이언트는 관련 Azure Queue에 메시지를 업로드하여 데이터를 수집합니다. 사후 실패의 경우 큐 URI, '오류' 필드의 실패 이유 및 'InnerException' 필드의 내부 예외가 포함된 예외가 발생합니다.

### <a name="dataformatnotspecifiedexception"></a>데이터 형식NotNot지정예외
데이터 형식이 필요하지만 IngestionProperties에 지정되지 않은 경우 발생합니다.

기본 클래스: 인제스트클라이언트예외

추가 정보:

스트림에서 수집할 때 데이터를 제대로 수집하려면 [IngestionProperties에](kusto-ingest-client-reference.md#class-kustoingestionproperties) 데이터 형식을 지정해야 합니다. 이 예외는 IngestionProperties.Format을 지정하지 않은 경우에 발생합니다.

### <a name="invaliduriingestclientexception"></a>무효
잘못된 Blob URI가 섭취 원본으로 제출되었을 때 발생합니다.

기본 클래스: 인제스트클라이언트예외

### <a name="compressfileingestclientexception"></a>압축 파일링게스트클라이언트예외
인제스트 클라이언트가 인비전에 제공된 파일을 압축하지 못했을 때 발생했습니다.

기본 클래스: 인제스트클라이언트예외

추가 정보:

파일은 파일을 쓰기 전에 압축됩니다. 이 예외는 파일을 압축하려는 시도가 실패할 때 발생합니다.

### <a name="uploadfiletotempblobingestclientexception"></a>업로드파일토템블로빙게스트클라이언트예외
인제스트 클라이언트가 인비전에 대해 제공된 소스를 임시 Blob에 업로드하지 못했을 때 발생합니다.

기본 클래스: 인제스트클라이언트예외

### <a name="sizelimitexceededingestclientexception"></a>크기제한초과클라이언트예외
섭취 소스가 너무 클 때 발생합니다.

기본 클래스: 인제스트클라이언트예외

필드:

|속성|Type|의미       
|-----------|----|------------------------------|
|크기| `long`| 섭취 소스의 크기
|MaxSize| `long`| 섭취가 허용되는 최대 크기

추가 정보:

섭취 소스가 최대 크기 4GB를 초과하면 예외가 throw됩니다. 크기 유효성 검사는 [InestionProperties 클래스의](kusto-ingest-client-reference.md#class-kustoingestionproperties)IgnoreSizeLimit 플래그에 의해 재정의될 수 있지만 [1GB보다 큰 단일 소스를 섭취하는](about-kusto-ingest.md#ingestion-best-practices)것은 권장되지 않습니다.

### <a name="uploadfiletotempblobingestclientexception"></a>업로드파일토템블로빙게스트클라이언트예외
인제스트 클라이언트가 인비전에 제공된 파일을 임시 Blob에 업로드하지 못했을 때 발생했습니다.

기본 클래스: 인제스트클라이언트예외

### <a name="directingestclientexception"></a>연출클라이언트예외
직접 섭취를 수행하는 동안 일반적인 오류가 발생했을 때 발생합니다.

기본 클래스: 인제스트클라이언트예외

### <a name="queuedingestclientexception"></a>큐에 대기되는 가장 스트클라이언트예외
큐에 대기된 섭취를 수행하는 동안 오류가 발생했을 때 발생했습니다.

기본 클래스: 인제스트클라이언트예외

### <a name="ingestclientaggregateexception"></a>인제스트클라이언트집계예외
섭취 중에 하나 이상의 오류가 발생했을 때 발생했습니다.

기본 클래스: [집계 예외](https://msdn.microsoft.com/library/system.aggregateexception(v=vs.110).aspx)

필드:

|속성|Type|의미       
|-----------|----|------------------------------|
|인비시오류| `IList<IngestClientException>`| 수집을 시도하는 동안 발생한 오류 및 관련 소스
|IsGlobalError| `bool`| 모든 소스에 대해 예외가 발생했는지 여부를 나타냅니다.

## <a name="errors-in-native-code"></a>네이티브 코드 오류
Kusto 엔진은 네이티브 코드로 작성됩니다. 네이티브 코드의 오류에 대한 자세한 내용은 [네이티브 코드의 오류를](../../concepts/errorsinnativecode.md) 참조하십시오.