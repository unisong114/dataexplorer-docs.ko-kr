---
title: 스트리밍 수집 HTTP 요청 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기에서 스트리밍 수집 HTTP 요청에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: d14987806bbc62dbc79112700bd5b88aaa6676c3
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81503012"
---
# <a name="streaming-ingestion-http-request"></a>스트리밍 생성 HTTP 요청

## <a name="request-verb-and-resource"></a>동사 및 리소스 요청

|작업    |HTTP 동사|HTTP 리소스                                               |
|----------|---------|------------------------------------------------------------|
|수집    |POST     |`/v1/rest/ingest/{database}/{table}?{additional parameters}`|

## <a name="request-parameters"></a>요청 매개 변수

| 매개 변수    |  Description                                                                                                |
|--------------|-------------------------------------------------------------------------------------------------------------|
| `{database}` | **필수** 인시션 요청에 대한 대상 데이터베이스의 이름                                          |
| `{table}`    | **필수** 인시션 요청에 대한 대상 테이블의 이름                                             |

## <a name="additional-parameters"></a>추가 매개 변수
url 쿼리: `{name}` = `{value}` & 문자로 분리된 쌍으로 추가 매개 변수의 서식이 지정됩니다.


| 매개 변수    |  Description                                                                                                |
|--------------|-------------------------------------------------------------------------------------------------------------|
|`streamFormat`| **필수** 요청 본문에 데이터의 형식을 지정합니다. 값은 다음 중 하나여야`Tsv``Scsv``SOHsv`합니다.`Psv``Json``SingleJson``MultiJson``Avro` `Csv` 자세한 내용은 [지원되는 데이터 형식을](https://docs.microsoft.com/azure/data-explorer/ingestion-supported-formats)참조하십시오.|
|`mappingName` | **필수** `streamFormat` 중 하나인 `Json` `SingleJson`경우 `MultiJson` `Avro`. 또는 , **그렇지 않으면 선택 사항입니다.** 값은 테이블에 정의된 미리 생성된 인비많이레이션 매핑의 이름이어야 합니다. 데이터 매핑에 대한 자세한 내용은 [데이터 매핑 을](../../management/mappings.md)참조하십시오. 테이블에서 미리 만들어진 매핑을 관리하는 방법은 [다음과 같습니다.](../../management/create-ingestion-mapping-command.md) |
              

예를 들어 CSV 형식의 데이터를 데이터베이스의 `Logs` `Test` 테이블에 수집하려면 다음 요청 줄을 사용합니다.

```
POST https://help.kusto.windows.net/v1/rest/ingest/Test/Logs?streamFormat=Csv HTTP/1.1
```

미리 만들어진 매핑으로 JSON 형식의 데이터를 수집합니다.`mylogmapping`

```
POST https://help.kusto.windows.net/v1/rest/ingest/Test/Logs?streamFormat=Json&mappingName=mylogmapping HTTP/1.1
```


(포함할 요청 헤더 및 본문에 대한 아래를 참조하십시오.)

## <a name="request-headers"></a>헤더 요청

다음 표에는 쿼리 및 관리 작업을 수행하는 데 사용되는 공통 헤더가 포함되어 있습니다.

|표준 헤더  |Description                                                                                                              |
|------------------|------------------------------------------------------------------------------------------------------------------------|
|`Accept`          |**선택 사항입니다**. `application/json`로 설정합니다.                                                                           |
|`Accept-Encoding` |**선택 사항입니다**. 지원되는 인코딩은 `deflate`및 . `gzip`                                                             |
|`Authorization`   |**필수 사항입니다**. [인증을](./authentication.md)참조하십시오.                                                                |
|`Connection`      |**선택 사항입니다**. 활성화하는 것이 `Keep-Alive` 좋습니다.                                                           |
|`Content-Length`  |**선택 사항입니다**. 알려진 경우 요청 본문 길이를 지정하는 것이 좋습니다.                                   |
|`Content-Encoding`|**선택 사항입니다**. gzip 압축이 `gzip` 필요한 경우 본체로 설정할 수 있습니다.                                 |
|`Expect`          |**선택 사항입니다**. `100-Continue`로 설정할 수 있습니다.                                                                             |
|`Host`            |**필수 사항입니다**. 요청이 전송된 정규화된 도메인 이름(예: `help.kusto.windows.net`)으로 설정합니다.|

다음 표에는 쿼리 및 관리 작업을 수행할 때 사용되는 일반적인 사용자 지정 헤더가 포함되어 있습니다. 달리 명시되지 않는 한 이러한 헤더는 원격 분석 목적으로만 사용되며 기능에 영향을 미치지 않습니다.

모든 헤더는 **선택 사항입니다.** 그러나 사용자 지정 헤더를 `x-ms-client-request-id` 지정하는 것이 **좋습니다.** 일부 시나리오(예: 실행 중인 쿼리 취소)에서는 요청을 식별하는 데 사용되는 이 헤더가 **필수입니다.**


|사용자 지정 헤더           |Description                                                                                               |
|------------------------|----------------------------------------------------------------------------------------------------------|
|`x-ms-app`              |요청을 하는 응용 프로그램의 (친숙한) 이름입니다.                                                |
|`x-ms-user`             |요청을 하는 사용자의 (친숙한) 이름입니다.                                                       |
|`x-ms-user-id`          |`x-ms-user`와 같습니다.                                                                                      |
|`x-ms-client-request-id`|요청의 고유 식별자                                                                      |
|`x-ms-client-version`   |요청을 하는 클라이언트에 대한 (친숙한) 버전 식별자입니다.                                      |

## <a name="body"></a>본문

본체는 수집할 실제 데이터입니다. 텍스트 형식은 UTF-8 인코딩을 사용합니다.

## <a name="examples"></a>예

다음 예제에서는 인제스트 JSON 콘텐츠에 대한 HTTP POST 요청을 보여 주며 다음과 같은 예제를 보여 주며 다음과 같은 예제를 보여 주며 다음과 같은 예제를 보여 주며,

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

다음 예제에서는 압축된 동일한 데이터를 수집하기 위한 HTTP POST 요청을 보여 주며

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