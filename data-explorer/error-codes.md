---
title: Azure 데이터 탐색기의 수집 오류 코드
description: 이 항목에서는 Azure의 수집 오류 코드를 나열 데이터 탐색기
author: orspod
ms.author: orspodek
ms.reviewer: vladikbr
ms.service: data-explorer
ms.topic: reference
ms.date: 11/11/2020
ms.openlocfilehash: 18ac718edd50c804f71b9b82cbffb5b2b7bb2e24
ms.sourcegitcommit: 202357f866801aafd92e3e29a84bb312e17aebc7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/09/2020
ms.locfileid: "96933778"
---
# <a name="ingestion-error-codes-in-azure-data-explorer"></a>Azure 데이터 탐색기의 수집 오류 코드

다음 목록에는 [수집 중에 발생 하는 오류](ingest-data-overview.md)코드가 포함 되어 있습니다. 클러스터에서 실패 한 수집 [진단 로그](using-diagnostic-logs.md#ingestion-logs-schema) 를 사용 하도록 설정 하면 **실패** 한 수집 작업 로그에서 오류 코드를 볼 수 있습니다. 수집 **결과** [메트릭을](using-metrics.md#ingestion-metrics) 모니터링 하 여 수집 오류의 **범주** 를 볼 수도 있지만 특정 오류 코드는 볼 수 없습니다. 아래 오류는 이러한 범주로 구성 됩니다. 

> [!NOTE]
> 일시적인 오류일 경우 수집을 다시 시도 하면 성공할 수 있습니다.

## <a name="category-badformat"></a>범주: BadFormat

|오류 메시지                                 |Description                                           |영구/임시|
|---|---|---|
|Stream_WrongNumberOfFields                        |입력 레코드의 필드 수가 일치 하지 않습니다. HRESULT: 0x80DA0008      |영구           |
|Stream_ClosingQuoteMissing                        |CSV 형식이 잘못 되었습니다. 닫는 따옴표가 없습니다. HRESULT: 0x80DA000b            |영구           |
|BadRequest_InvalidBlob                            |Blob이 잘못 되었습니다.                                                              |영구           |
|BadRequest_EmptyArchive                           |보관 파일이 비어 있습니다.                                                             |영구           |
|BadRequest_InvalidArchive                         |보관 파일이 잘못 되었습니다.                                                           |영구           |
|BadRequest_InvalidMapping                         |수집 매핑을 구문 분석 하지 못했습니다.<br>수집 매핑을 작성 하는 방법에 대 한 자세한 내용은 [데이터 매핑](./kusto/management/mappings.md)을 참조 하세요.   |영구           |
|BadRequest_InvalidMappingReference                |매핑 참조가 잘못 되었습니다.            |영구           |
|BadRequest_FormatNotSupported                     |형식은 지원 되지 않습니다. 특정 데이터 연결에서 지원 하지 않는 형식을 사용 하 고 있기 때문일 수 있습니다. <br>수집을 위해 Azure 데이터 탐색기에서 지 원하는 데이터 형식에 대 한 자세한 내용은 [지원 되는 데이터 형식](ingestion-supported-formats.md)을 참조 하세요. |영구          |
|BadRequest_InconsistentMapping                    |지원 되는 수집 매핑은 기존 테이블 스키마와 일치 하지 않습니다. |영구           |
|BadRequest_UnexpectedCharacterInInputStream       |입력 스트림에 예기치 않은 문자가 있습니다.                                     |영구           |

## <a name="category-badrequest"></a>범주: BadRequest

|오류 메시지                                 |Description                                           |영구/임시|
|---|---|---|
|BadRequest_EmptyBlob                              |Blob이 비어 있습니다.                                                               |영구           |
|BadRequest_EmptyBlobUri                           |Blob Uri가 비어 있습니다.                                                           |영구           |
|BadRequest_DuplicateMapping                       |수집 속성에 ingestionMapping 및 ingestionMappingReference가 모두 포함 되어 있으며이는 유효 하지 않습니다.              |영구          |
|BadRequest_InvalidOrEmptyTableName                |테이블 이름이 비어 있거나 잘못 되었습니다.<br>Azure 데이터 탐색기 명명 규칙에 대 한 자세한 내용은 [엔터티 이름](./kusto/query/schema-entities/entity-names.md)을 참조 하세요.    |영구          |
|BadRequest_EmptyDatabaseName                      |데이터베이스 이름이 비어 있습니다.             |영구          |
|BadRequest_EmptyMappingReference                  |일부 형식은 수집 매핑을 수집 하 고 매핑 참조는 비어 있어야 합니다.<br>매핑에 대 한 자세한 내용은 [데이터 매핑](./kusto/management/mappings.md)을 참조 하세요.        |영구           |
|Download_BadRequest                               |잘못 된 요청으로 인해 Azure Storage에서 소스를 다운로드 하지 못했습니다.    |영구           |
|BadRequest_MissingMappingtFailure                 |Avro 및 Json 형식은 ingestionMapping 또는 ingestionMappingReference 매개 변수를 사용 하 여 수집 여야 합니다.         |영구           |
|Stream_NoDataToIngest                             |수집할 데이터를 찾을 수 없습니다.<br>JSON 형식의 데이터의 경우이 오류는 JSON 형식이 잘못 되었음을 나타낼 수 있습니다.        |영구          |
|Stream_DynamicPropertyBagTooLarge                 |데이터의 동적 열에 너무 많은 값이 포함 되어 있습니다. HRESULT: 0x80DA000E         |영구          |
|General_BadRequest                                |잘못된 요청입니다.            |영구           |
|BadRequest_CorruptedMessage                       |메시지가 손상 되었습니다.    |영구           |
|BadRequest_SyntaxError                            |요청 구문 오류입니다.     |영구           |
|BadRequest_ZeroRetentionPolicyWithNoUpdatePolicy  |테이블에 보존 정책이 없거나 업데이트 정책에 대 한 원본 테이블이 아닙니다.    |영구           |
|BadRequest_CreationTimeEarlierThanSoftDeletePeriod|수집을 위해 지정 된 생성 시간은 내에 없습니다 `SoftDeletePeriod` .<br>에 대 한 자세한 내용은 `SoftDeletePeriod` [정책 개체](./kusto/management/retentionpolicy.md#the-policy-object)를 참조 하세요.  |영구   |
|BadRequest_NotSupported                           |요청이 지원 되지 않습니다.    |영구           |
|Download_SourceNotFound                           |Azure Storage에서 소스를 다운로드 하지 못했습니다. 소스를 찾을 수 없습니다.       |영구       |
|BadRequest_EntityNameIsNotValid                   |엔터티 이름이 잘못 되었습니다.<br>Azure 데이터 탐색기 명명 규칙에 대 한 자세한 내용은 [엔터티 이름](./kusto/query/schema-entities/entity-names.md)을 참조 하세요.    |영구           |
|BadRequest_MalformedIngestionProperty              |수집 속성의 형식이 잘못 되었습니다.    |영구           |
| BadRequest_IngestionPropertyNotSupportedInThisContext | 이 컨텍스트에서는 수집 속성이 지원 되지 않습니다.| 영구

## <a name="category-dataaccessnotauthorized"></a>범주: DataAccessNotAuthorized

|오류 메시지                                 |Description                                           |영구/임시|
|---|---|---|
|Download_AccessConditionNotSatisfied              |Azure storage에서 소스를 다운로드 하지 못했습니다. 액세스 조건이 충족 되지 않았습니다.     |영구           |
|Download_Forbidden                                |Azure storage에서 소스를 다운로드 하지 못했습니다. 액세스가 금지되었습니다.    |영구           |
|Download_AccountNotFound                          |Azure storage에서 소스를 다운로드 하지 못했습니다. 계정을 찾을 수 없습니다.    |영구           |
|BadRequest_TableAccessDenied                      |테이블에 대 한 액세스가 거부 되었습니다.<br>자세한 내용은 [Kusto의 역할 기반 권한 부여](./kusto/management/access-control/role-based-authorization.md)를 참조 하세요.     |영구           |
|BadRequest_DatabaseAccessDenied                   |데이터베이스에 대 한 액세스가 거부 되었습니다.<br>자세한 내용은 [Kusto의 역할 기반 권한 부여](./kusto/management/access-control/role-based-authorization.md)를 참조 하세요.                        |영구           |

## <a name="category-downloadfailed"></a>범주: DownloadFailed

|오류 메시지                                 |Description                                           |영구/임시|
|---|---|---|
|Download_NotTransient                             |Azure storage에서 소스를 다운로드 하지 못했습니다. 일시적인 오류가 발생 했습니다.                   |영구           |
|Download_UnknownError                             |Azure storage에서 소스를 다운로드 하지 못했습니다. 알 수 없는 오류 발생              |Transient           |


## <a name="category-entitynotfound"></a>범주: EntityNotFound

|오류 메시지                                 |Description                                           |영구/임시|
|---|---|---|
|BadRequest_MappingReferenceWasNotFound            |매핑 참조를 찾을 수 없습니다.   |영구           |
|BadRequest_DatabaseNotExist                       |데이터베이스가 존재 하지 않습니다.          |영구           |
|BadRequest_TableNotExist                          |테이블이 없습니다.          |영구           |
|BadRequest_EntityNotFound                         |Azure 데이터 탐색기 엔터티 (예: 매핑, 데이터베이스 또는 테이블)를 찾을 수 없습니다.           |영구           |

## <a name="category-filetoolarge"></a>범주: FileTooLarge

|오류 메시지                                 |Description                                           |영구/임시|
|---|---|---|
|Stream_InputStreamTooLarge                        |입력 데이터의 전체 크기 또는 데이터의 단일 필드가 너무 깁니다. HRESULT: 0x80DA0009                 |영구          |
|BadRequest_FileTooLarge                           |Blob 크기가 수집에 허용 된 크기 제한을 초과 했습니다.<br>수집에 대 한 크기 제한에 대 한 자세한 내용은 [Azure 데이터 탐색기 데이터 수집 개요](ingest-data-overview.md#comparing-ingestion-methods-and-tools)를 참조 하세요. |영구           |

## <a name="category-internalserviceerror"></a>범주: InternalServiceError

|오류 메시지                                 |Description                                           |영구/임시|
|---|---|---|
|General_InternalServerError                       |내부 서버 오류가 발생했습니다.                     |Transient          |
|General_TransientSchemaMismatch                   |수집을 시작할 때 대상 테이블의 스키마가 수집을 커밋할 때 스키마와 일치 하지 않습니다.         |Transient           |
|제한 시간                                            |시간 제한으로 인해 작업이 중단 되었습니다.     |Transient           |
|BadRequest_MessageExhausted                       |수집이 최대 재시도 횟수 또는 최대 재시도 기간에 도달 했으므로 데이터를 수집 하지 못했습니다.<br>수집을 다시 시도 하면 성공할 수 있습니다.   |Transient          |

## <a name="category-updatepolicyfailure"></a>범주: UpdatePolicyFailure

|오류 메시지                                 |Description                                           |영구/임시|
|---|---|---|
|UpdatePolicy_QuerySchemaDoesNotMatchTableSchema   |업데이트 정책을 호출 하지 못했습니다. 쿼리 스키마가 테이블 스키마와 일치 하지 않습니다.     |영구           |
|UpdatePolicy_FailedDescendantTransaction          |업데이트 정책을 호출 하지 못했습니다. 실패 한 하위 트랜잭션 업데이트 정책입니다.    |Transient           |
|UpdatePolicy_IngestionError                       |업데이트 정책을 호출 하지 못했습니다. 수집 오류가 발생 했습니다.<br>업데이트 정책의 원본 테이블에 오류가 보고 됩니다.     |Transient          |
|UpdatePolicy_UnknownError                         |업데이트 정책을 호출 하지 못했습니다. 알 수 없는 오류 발생.<br>업데이트 정책의 대상 테이블에 오류가 보고 됩니다.    |Transient           |
|UpdatePolicy_Cyclic_Update_Not_Allowed         |업데이트 정책을 호출 하지 못했습니다. 순환 업데이트는 허용 되지 않습니다.      |영구           |

## <a name="category-useraccessnotauthorized"></a>범주: UserAccessNotAuthorized 있음

|오류 메시지                                 |Description                                           |영구/임시|
|---|---|---|
|BadRequest_InvalidKustoIdentityToken              |잘못 된 Kusto id 토큰입니다.                           |영구           |
|사용할 수 없음                                          |요청을 실행 하는 데 필요한 보안 권한이 없습니다. | 영구|

## <a name="category-unknown"></a>범주: 알 수 없음

|오류 메시지                                 |Description                                           |영구/임시|
|---|---|---|
|알 수 없음                                            |알 수 없는 오류 발생.                             |Transient          |
