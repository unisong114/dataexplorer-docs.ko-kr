---
title: 쿼리/관리 HTTP 요청-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 쿼리/관리 HTTP 요청을 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 01/27/2020
ms.openlocfilehash: 2c6efc03ea252eba5ed63e99d9214e59113856e9
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/13/2020
ms.locfileid: "83373573"
---
# <a name="querymanagement-http-request"></a>쿼리/관리 HTTP 요청

## <a name="request-verb-and-resource"></a>요청 동사 및 리소스

|작업    |HTTP 동사|HTTP 리소스   |
|----------|---------|----------------|
|쿼리     |GET      |`/v1/rest/query`|
|쿼리     |POST     |`/v1/rest/query`|
|쿼리 v2  |GET      |`/v2/rest/query`|
|쿼리 v2  |POST     |`/v2/rest/query`|
|관리|POST     |`/v1/rest/mgmt` |

예를 들어 서비스 끝점에 제어 명령 ("관리")을 보내려면 다음 요청 줄을 사용 합니다.

```
POST https://help.kusto.windows.net/v1/rest/mgmt HTTP/1.1
```

포함할 요청 헤더 및 본문은 아래를 참조 하세요.

## <a name="request-headers"></a>요청 헤더

다음 표에서는 쿼리 및 관리 작업에 사용 되는 공통 헤더를 포함 합니다.

|표준 헤더  |설명                                                                                 |필수/선택 |
|-----------------|--------------------------------------------------------------------------------------------|------------------|
|`Accept`         |`application/json`                                                                   |필수          |
|`Accept-Encoding`|지원 되는 인코딩은 및입니다. `gzip``deflate`                                                |Optional          |
|`Authorization`  |[인증](./authentication.md) 참조                                                   |필수          |
|`Connection`     |사용 하도록 설정 하는 것이 좋습니다.`Keep-Alive`                                                   |Optional          |
|`Content-Length` |알려진 경우 요청 본문 길이를 지정 하는 것이 좋습니다.                            |Optional          |
|`Content-Type`   |을 사용 하 여로 설정 `application/json``charset=utf-8`                                              |필수          |
|`Expect`         |로 설정할 수 있습니다.`100-Continue`                                                                |Optional          |
|`Host`           |요청을 보낸 정규화 된 도메인 이름 (예:)으로 설정 합니다. `help.kusto.windows.net` |필수|

다음 표에서는 쿼리 및 관리 작업에 사용 되는 일반적인 사용자 지정 헤더를 포함 합니다. 별도로 지정 하지 않는 한 이러한 헤더는 원격 분석 용도로만 사용 되며 기능에는 영향을 주지 않습니다.

모든 헤더는 선택 사항입니다. 사용자 지정 헤더를 지정 하는 것이 좋습니다 `x-ms-client-request-id` . 실행 중인 쿼리를 취소 하는 등의 일부 시나리오에서는 요청을 식별 하는 데 사용 되기 때문에이 헤더가 필요 합니다.

|사용자 지정 헤더           |설명                                                                                               |
|------------------------|----------------------------------------------------------------------------------------------------------|
|`x-ms-app`              |요청을 수행 하는 응용 프로그램의 친숙 한 이름입니다.                                                 |
|`x-ms-user`             |요청을 수행 하는 사용자의 이름입니다.                                                        |
|`x-ms-user-id`          |`x-ms-user`과 동일                                                                                       |
|`x-ms-client-request-id`|요청에 대 한 고유 식별자입니다.                                                                       |
|`x-ms-client-version`   |요청을 수행 하는 클라이언트에 대 한 친숙 한 버전 식별자입니다.                                       |
|`x-ms-readonly`         |지정 된 경우 요청을 읽기 전용 모드로 강제로 실행 하 여 시간이 오래 지속 되는 변경 작업을 수행 하지 못하도록 합니다. |

## <a name="request-parameters"></a>요청 매개 변수

요청에서 다음 매개 변수를 전달할 수 있습니다. GET 또는 POST를 사용 하는지에 따라 요청에서 쿼리 매개 변수로 또는 본문의 일부로 인코딩됩니다.

|매개 변수   |설명                                                                                 |필수/선택 |
|------------|--------------------------------------------------------------------------------------------|------------------|
|`csl`       |실행할 쿼리 또는 제어 명령의 텍스트입니다.                                             |필수          |
|`db`        |쿼리 또는 제어 명령의 대상인 범위에 있는 데이터베이스의 이름입니다.            |일부 제어 명령의 경우 선택 사항입니다. <br>다른 명령 및 모든 쿼리에 필요 합니다. </br>                                                                   |
|`properties`|요청이 처리 되는 방법과 그 결과를 수정 하는 클라이언트 요청 속성을 제공 합니다. 자세한 내용은 [클라이언트 요청 속성](../netfx/request-properties.md) 을 참조 하세요.                                               | Optional         |

## <a name="get-query-parameters"></a>쿼리 매개 변수 가져오기

GET을 사용 하는 경우 요청의 쿼리 매개 변수는 요청 매개 변수를 지정 합니다.

## <a name="body"></a>본문

POST를 사용 하는 경우 요청 본문은 요청 매개 변수의 값을 사용 하 여 u t f-8로 인코딩된 단일 JSON 문서입니다.

## <a name="examples"></a>예

이 예제에서는 쿼리에 대 한 HTTP POST 요청을 보여 줍니다.

```txt
POST https://help.kusto.windows.net/v2/rest/query HTTP/1.1
```

요청 헤더

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

요청 본문

```json
{
  "db":"Samples",
  "csl":"print Test=\"Hello, World!\"",
  "properties":"{\"Options\":{\"queryconsistency\":\"strongconsistency\"},\"Parameters\":{},\"ClientRequestId\":\"MyApp.Query;e9f884e4-90f0-404a-8e8b-01d883023bf1\"}"
}
```

이 예제에서는 [말아 넘기기](https://curl.haxx.se/)를 사용 하 여 위의 쿼리를 전송 하는 요청을 만드는 방법을 보여 줍니다.

1. 인증을 위한 토큰을 가져옵니다.

    `AAD_TENANT_NAME_OR_ID` `AAD_APPLICATION_ID` `AAD_APPLICATION_KEY` [AAD 응용 프로그램 인증](../../management/access-control/how-to-provision-aad-app.md) 을 설정한 후, 및를 관련 값으로 바꿉니다.

    ```
    curl "https://login.microsoftonline.com/AAD_TENANT_NAME_OR_ID/oauth2/token" \
      -F "grant_type=client_credentials" \
      -F "resource=https://help.kusto.windows.net" \
      -F "client_id=AAD_APPLICATION_ID" \
      -F "client_secret=AAD_APPLICATION_KEY"
    ```

    이 코드 조각에서는 전달자 토큰을 제공 합니다.

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

1. 요청에서 쿼리 끝점에 대 한 전달자 토큰을 사용 합니다.

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

1. [이 사양](response.md)에 따라 응답을 읽습니다.
