---
title: 쿼리/관리 HTTP 요청 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기에서 쿼리/관리 HTTP 요청에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 01/27/2020
ms.openlocfilehash: 71fac7122ca51755beaa09ce3867143806e4f2b3
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81502723"
---
# <a name="querymanagement-http-request"></a>쿼리/관리 HTTP 요청

## <a name="request-verb-and-resource"></a>동사 및 리소스 요청

|작업    |HTTP 동사|HTTP 리소스   |
|----------|---------|----------------|
|쿼리     |GET      |`/v1/rest/query`|
|쿼리     |POST     |`/v1/rest/query`|
|쿼리 v2  |GET      |`/v2/rest/query`|
|쿼리 v2  |POST     |`/v2/rest/query`|
|관리|POST     |`/v1/rest/mgmt` |

예를 들어 제어 명령("관리")을 서비스 끝점으로 보내려면 다음 요청 줄을 사용합니다.

```
POST https://help.kusto.windows.net/v1/rest/mgmt HTTP/1.1
```

(포함할 요청 헤더 및 본문에 대한 아래를 참조하십시오.)

## <a name="request-headers"></a>헤더 요청

다음 표에는 쿼리 및 관리 작업을 수행하는 데 사용되는 공통 헤더가 포함되어 있습니다.

|표준 헤더  |Description                                                                                                                    |
|-----------------|-------------------------------------------------------------------------------------------------------------------------------|
|`Accept`         |**필수 사항입니다**. `application/json`로 설정합니다.                                                                                  |
|`Accept-Encoding`|**선택 사항입니다**. 지원되는 인코딩은 `deflate`및 . `gzip`                                                                    |
|`Authorization`  |**필수 사항입니다**. [인증을](./authentication.md)참조하십시오.                                                                       |
|`Connection`     |**선택 사항입니다**. 활성화하는 것이 `Keep-Alive` 좋습니다.                                                                  |
|`Content-Length` |**선택 사항입니다**. 알려진 경우 요청 본문 길이를 지정하는 것이 좋습니다.                                          |
|`Content-Type`   |**필수 사항입니다**. 을 으로 `application/json` `charset=utf-8`설정합니다.                                                             |
|`Expect`         |**선택 사항입니다**. `100-Continue`로 설정할 수 있습니다.                                                                                    |
|`Host`           |**필수 사항입니다**. 요청이 전송된 정규화된 도메인 `help.kusto.windows.net`이름(예:)으로 설정합니다.|

다음 표에는 쿼리 및 관리 작업을 수행할 때 사용되는 일반적인 사용자 지정 헤더가 포함되어 있습니다. 달리 명시되지 않는 한 이러한 헤더는 원격 분석 목적으로만 사용되며 기능에 영향을 미치지 않습니다.

모든 헤더는 **선택 사항입니다.** 사용자 지정 헤더를 `x-ms-client-request-id` 지정하는 것이 **좋습니다.** 일부 시나리오(예: 실행 중인 쿼리 취소)에서는 요청을 식별하는 데 사용되므로 이 헤더가 **필수입니다.**


|사용자 지정 헤더           |Description                                                                                               |
|------------------------|----------------------------------------------------------------------------------------------------------|
|`x-ms-app`              |요청을 하는 응용 프로그램의 (친숙한) 이름입니다.                                                |
|`x-ms-user`             |요청을 하는 사용자의 (친숙한) 이름입니다.                                                       |
|`x-ms-user-id`          |`x-ms-user`와 같습니다.                                                                                      |
|`x-ms-client-request-id`|요청의 고유 식별자                                                                      |
|`x-ms-client-version`   |요청을 하는 클라이언트에 대한 (친숙한) 버전 식별자입니다.                                      |
|`x-ms-readonly`         |이 경우 읽기 전용 모드에서 실행하도록 요청을 강제로 실행합니다(오래 지속되는 변경을 방지).|

## <a name="request-parameters"></a>요청 매개 변수

다음 매개 변수는 요청에서 전달할 수 있습니다. GET 또는 POST사용 여부에 따라 요청에서 쿼리 매개 변수 또는 본문의 일부로 인코딩됩니다.

* `csl`: **필수** 매개 변수입니다. 실행할 쿼리 또는 제어 명령의 텍스트를 보유합니다.

* `db`: 일부 제어 명령에 대한 **선택적** 매개 변수이며 다른 제어 명령 및 모든 쿼리에 대한 **필수** 매개 변수입니다. 쿼리 또는 제어 명령의 대상인 데이터베이스인 "범위 내 데이터베이스"의 이름을 제공합니다.

* `properties`: **선택적** 매개 변수입니다. 요청이 처리되는 방법과 결과를 수정하는 다양한 클라이언트 요청 속성을 제공합니다. 전체 설명은 [클라이언트 요청 속성을](../netfx/request-properties.md)참조하십시오.

## <a name="get-query-parameters"></a>쿼리 매개 변수 가져옵니다.

GET을 사용하면 요청의 쿼리 매개 변수가 위에 언급된 요청 매개 변수를 지정합니다.

## <a name="body"></a>본문

POST를 사용하면 요청 본문이 UTF-8로 인코딩된 단일 JSON 문서로 위에 언급된 요청 매개 변수의 값을 제공합니다.

## <a name="examples"></a>예

다음 예제에서는 쿼리에 대한 HTTP POST 요청을 보여 주며 있습니다.

```txt
POST https://help.kusto.windows.net/v2/rest/query HTTP/1.1
```

헤더 요청:

```txt
Accept: application/json
Authorization: Bearer ...AzureActiveDirectoryAccessToken...
Accept-Encoding: deflate
Content-Type: application/json; charset=utf-8
Host: help.kusto.windows.net
x-ms-client-request-id: MyApp.Query;e9f884e4-90f0-404a-8e8b-01d883023bf1
x-ms-user-id: EARTH\davidbg
x-ms-app: MyApp
```

요청 본문 (명확성을 위해 도입 된 줄바인; 그들은 필요하지 않습니다):

```json
{
  "db":"Samples",
  "csl":"print Test=\"Hello, World!\"",
  "properties":"{\"Options\":{\"queryconsistency\":\"strongconsistency\"},\"Parameters\":{},\"ClientRequestId\":\"MyApp.Query;e9f884e4-90f0-404a-8e8b-01d883023bf1\"}"
}
```

다음 예제에서는 [curl을](https://curl.haxx.se/)사용 하 여 위의 쿼리를 보내는 요청을 만드는 방법을 보여 주어 :

1. 인증을 위한 토큰을 가져옵니다.

* `AAD_TENANT_NAME_OR_ID` [AAD 응용 프로그램 인증을](../../management/access-control/how-to-provision-aad-app.md) 설정한 후 을 `AAD_APPLICATION_ID` 대체하고 `AAD_APPLICATION_KEY` 관련 값으로

```
curl "https://login.microsoftonline.com/AAD_TENANT_NAME_OR_ID/oauth2/token" \
  -F "grant_type=client_credentials" \
  -F "resource=https://help.kusto.windows.net" \
  -F "client_id=AAD_APPLICATION_ID" \
  -F "client_secret=AAD_APPLICATION_KEY"
```

이렇게 하면 베어러 토큰이 표시됩니다.

```
{
  "token_type": "Bearer",
  "expires_in": "3599",
  "ext_expires_in":"3599", 
  "expires_on":"1578439805",
  "not_before":"1578435905",
  "resource":"https://help.kusto.windows.net",
  "access_token":"eyJ0...uXOQ"
}
```

2. 쿼리 끝점에 대한 요청에서 베어러 토큰을 사용합니다.

```
curl -d '{"db":"Samples","csl":"print Test=\"Hello, World!\"","properties":"{\"Options\":{\"queryconsistency\":\"strongconsistency\"}}"}"' \
-H "Accept: application/json" \
-H "Authorization: Bearer eyJ0...uXOQ" \
-H "Content-Type: application/json; charset=utf-8" \
-H "Host: help.kusto.windows.net" \
-H "x-ms-client-request-id: MyApp.Query;e9f884e4-90f0-404a-8e8b-01d883023bf1" \
-H "x-ms-user-id: EARTH\davidbg" \
-H "x-ms-app: MyApp" \
-X POST https://help.kusto.windows.net/v2/rest/query
```

3. [이 사양에](response.md)따라 응답을 읽으십시오.