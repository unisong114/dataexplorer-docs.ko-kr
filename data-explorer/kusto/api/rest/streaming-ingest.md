---
title: 스트리밍 수집 HTTP 요청-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 스트리밍 수집 HTTP 요청을 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: 672f924865cab14dff6c7d5319c3c34cca1a67ee
ms.sourcegitcommit: f6cf88be736aa1e23ca046304a02dee204546b6e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2020
ms.locfileid: "82862017"
---
# <a name="streaming-ingestion-http-request"></a>스트리밍 수집 HTTP 요청

## <a name="request-verb-and-resource"></a>요청 동사 및 리소스

|작업    |HTTP 동사|HTTP 리소스                                               |
|----------|---------|------------------------------------------------------------|
|수집    |POST     |`/v1/rest/ingest/{database}/{table}?{additional parameters}`|

## <a name="request-parameters"></a>요청 매개 변수

| 매개 변수    | Description                                                                 | 필수/선택 |
|--------------|-----------------------------------------------------------------------------|-------------------|
| `{database}` |   수집 요청에 대 한 대상 데이터베이스의 이름입니다.                     |  필수         |
| `{table}`    |   수집 요청에 대 한 대상 테이블의 이름입니다.                        |  필수         |

## <a name="additional-parameters"></a>추가 매개 변수

추가 매개 변수는 & 문자로 구분 `{name}={value}` 되는 URL 쿼리 쌍으로 형식이 지정 됩니다.

| 매개 변수    | Description                                                                          | 필수/선택   |
|--------------|--------------------------------------------------------------------------------------|---------------------|
|`streamFormat`| 요청 본문에 있는 데이터의 형식을 지정 합니다. 값 `CSV`은,`TSV`,`SCsv`,`SOHsv``PSV``JSON``MultiJSON``Avro`,,,,, 중 하나 여야 합니다.`SingleJSON` 자세한 내용은 [지원 되는 데이터 형식](https://docs.microsoft.com/azure/data-explorer/ingestion-supported-formats)을 참조 하세요.| 필수 |
|`mappingName` | 테이블에 정의 된 미리 만든 수집 매핑의 이름입니다. 자세한 내용은 [데이터 매핑](../../management/mappings.md)을 참조 하세요. 테이블에서 미리 생성 된 매핑을 관리 하는 방법은 [여기](../../management/create-ingestion-mapping-command.md)에 설명 되어 있습니다.| 선택 `streamFormat` 사항 이지만이 `JSON`,`SingleJSON`,`MultiJSON`또는 중 하나인 경우에는 필수입니다.`Avro`|  |
              
예를 들어 CSV 형식의 데이터를 데이터베이스 `Logs` `Test`의 테이블로 수집 하려면 다음을 사용 합니다.

```
POST https://help.kusto.windows.net/v1/rest/ingest/Test/Logs?streamFormat=Csv HTTP/1.1
```

미리 만든 매핑을 `mylogmapping`사용 하 여 JSON 형식 데이터를 수집 하려면 다음을 사용 합니다.

```
POST https://help.kusto.windows.net/v1/rest/ingest/Test/Logs?streamFormat=Json&mappingName=mylogmapping HTTP/1.1
```

## <a name="request-headers"></a>요청 헤더

다음 표에는 쿼리 및 관리 작업에 대 한 공용 헤더가 포함 되어 있습니다.

|표준 헤더   | Description                                                                               | 필수/선택 | 
|------------------|-------------------------------------------------------------------------------------------|-------------------|
|`Accept`          | 이 값을로 `application/json`설정 합니다.                                                     | Optional          |
|`Accept-Encoding` | 지원 되는 `gzip` 인코딩은 `deflate`및입니다.                                             | Optional          | 
|`Authorization`   | [인증](./authentication.md)을 참조 하세요.                                                | 필수          |
|`Connection`      | `Keep-Alive`을 사용하도록 설정합니다.                                                                      | Optional          |
|`Content-Length`  | 알려진 경우 요청 본문 길이를 지정 합니다.                                              | Optional          |
|`Content-Encoding`| 로 `gzip` 설정 되지만 본문은 gzip으로 압축 되어야 합니다.                                        | Optional          |
|`Expect`          | `100-Continue`로 설정합니다.                                                                    | Optional          |
|`Host`            | 을 요청을 보낸 도메인 이름 (예:, `help.kusto.windows.net`)으로 설정 합니다. | 필수          |

다음 표에서는 쿼리 및 관리 작업에 대 한 일반적인 사용자 지정 헤더를 포함 합니다. 별도로 지정 하지 않는 한 헤더는 원격 분석 용도로만 사용 되며 기능에는 영향을 주지 않습니다.

|사용자 지정 헤더           |Description                                                                           | 필수/선택 |
|------------------------|----------------------------------------------------------------------------------------------------------|
|`x-ms-app`              |요청을 수행 하는 응용 프로그램의 친숙 한 이름입니다.                            | Optional          |
|`x-ms-user`             |요청을 수행 하는 사용자의 이름입니다.                                   | Optional          |
|`x-ms-user-id`          |`x-ms-user`와 동일합니다.                                                                  | Optional          |
|`x-ms-client-request-id`|요청의 고유 식별자                                                  | Optional          |
|`x-ms-client-version`   |요청을 수행 하는 클라이언트에 대 한 친숙 한 버전 식별자입니다. 실행 중인 쿼리를 취소 하는 것과 같이 요청을 식별 하는 데 사용 되는 시나리오에 필요 합니다.                                                        | 선택/필수  |

## <a name="body"></a>본문

본문은 수집 실제 데이터입니다. 텍스트 형식은 UTF-8 인코딩을 사용 해야 합니다.

## <a name="examples"></a>예

다음 예제에서는 수집 JSON 콘텐츠에 대 한 HTTP POST 요청을 보여 줍니다.

```txt
POST https://help.kusto.windows.net/v1/rest/ingest/Test/Logs?streamFormat=Json&mappingName=mylogmapping HTTP/1.1
```

헤더 요청:

```txt
Authorization: Bearer ...AzureActiveDirectoryAccessToken...
Accept-Encoding: deflate
Accept-Encoding: gzip
Connection: Keep-Alive
Content-Length: 161
Host: help.kusto.windows.net
x-ms-client-request-id: MyApp.Ingest;5c0656b9-37c9-4e3a-a671-5f83e6843fce
x-ms-user-id: alex@contoso.com
x-ms-app: MyApp
```

본문 요청:

```txt
{"Timestamp":"2018-11-14 11:34","Level":"Info","EventText":"Nothing Happened"}
{"Timestamp":"2018-11-14 11:35","Level":"Error","EventText":"Something Happened"}
```

다음 예제에서는 동일한 압축 데이터를 수집에 대 한 HTTP POST 요청을 보여 줍니다.

```txt
POST https://help.kusto.windows.net/v1/rest/ingest/Test/Logs?streamFormat=Json&mappingName=mylogmapping HTTP/1.1
```

헤더 요청:

```txt
Authorization: Bearer ...AzureActiveDirectoryAccessToken...
Accept-Encoding: deflate
Accept-Encoding: gzip
Connection: Keep-Alive
Content-Length: 116
Content-Encoding: gzip
Host: help.kusto.windows.net
x-ms-client-request-id: MyApp.Ingest;5c0656b9-37c9-4e3a-a671-5f83e6843fce
x-ms-user-id: alex@contoso.com
x-ms-app: MyApp
```

본문 요청:

```
... binary data ...
```
