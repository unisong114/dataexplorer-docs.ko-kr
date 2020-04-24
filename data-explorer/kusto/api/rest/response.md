---
title: 쿼리/관리 HTTP 응답 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기에서 쿼리/관리 HTTP 응답을 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 11/05/2018
ms.openlocfilehash: 13937bbc9d17ed570c40926497ccf9b5c942fe72
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81503080"
---
# <a name="querymanagement-http-response"></a>쿼리/관리 HTTP 응답

## <a name="response-status"></a>응답 상태

HTTP 응답 상태 줄은 HTTP 표준 응답 코드를 따릅니다(예: 200은 성공을 나타냅니다). 다음 상태 코드가 현재 사용 중이지만 유효한 HTTP 코드가 반환될 수 있습니다.

|코드|하위 코드       |Description                                    |
|----|---------------|-----------------------------------------------|
|100 |계속       |클라이언트는 요청을 계속 보낼 수 있습니다.       |
|200 |확인             |요청이 성공적으로 처리를 시작했습니다.       |
|400 |BadRequest     |요청이 잘못 되어 실패 (영구적으로) 입니다.|
|401 |권한 없음   |클라이언트는 먼저 인증해야 합니다.            |
|403 |사용할 수 없음      |클라이언트 요청이 거부되었습니다.                      |
|404 |NotFound       |요청은 존재하지 않는 엔터티를 참조합니다.      |
|413 |페이로드너무 큰|요청 페이로드가 한도를 초과했습니다.               |
|429 |TooManyRequests|제한으로 인해 요청이 거부되었습니다.     |
|504 |시간 제한        |요청에 시간 시간이 만료되었습니다.                         |
|520 |ServiceError   |서비스에서 요청을 처리하는 오류가 발생했습니다.|

> [!NOTE]
> 200 상태 코드는 요청 처리가 성공적으로 시작되었음을 나타내며 성공적으로 완료되지 않았다는 것을 인식하는 것이 중요합니다. 요청 처리 중에 발생했지만 200회 반환된 후 발생하는 오류는 "부분 쿼리 오류"라고 하며, 이 경우 특수 지표가 응답 스트림에 주입되어 클라이언트에게 발생 사실을 알립니다.

## <a name="response-headers"></a>응답 헤더

다음 사용자 지정 헤더가 반환됩니다.

|사용자 지정 헤더           |Description                                                                                               |
|------------------------|----------------------------------------------------------------------------------------------------------|
|`x-ms-client-request-id`|동일한 이름의 요청 헤더 또는 일부 고유 식별자로 전송된 고유 요청 식별자입니다.     |
|`x-ms-activity-id`      |요청에 대한 전역 고유 상관 관계 식별자(서비스에서 만든 값)입니다.                        |

## <a name="response-body"></a>응답 본문

상태 코드가 200이면 응답 본문이 쿼리 또는 제어 명령의 결과를 직사각형 테이블의 시큐크로 인코딩하는 JSON 문서입니다.
자세한 내용은 다음을 참조하세요.

> [!NOTE]
> 이 테이블 시퀀스는 SDK에 의해 반영됩니다. 예를 들어 .NET Framework Kusto.Data 라이브러리를 사용하는 경우 테이블 시퀀스는 SDK에서 반환된 개체의 `System.Data.IDataReader` 결과가 됩니다.

상태 코드가 4xx 또는 5xx 오류(401이 아닌 제외)를 나타내는 경우 응답 본체는 Microsoft REST API [지침을](https://github.com/microsoft/api-guidelines)준수하여 오류 세부 정보를 인코딩하는 JSON 문서입니다.

> [!NOTE]
> 헤더가 `Accept` 요청에 포함되지 않은 경우 오류의 응답 본문이 반드시 JSON 문서가 아닙니다.

## <a name="json-encoding-of-a-sequence-of-tables"></a>테이블 시퀀스의 JSON 인코딩

테이블 시퀀스의 JSON 인코딩은 다음 이름/값 쌍을 가진 단일 JSON 속성 백입니다.

|이름  |값                              |
|------|-----------------------------------|
|테이블|테이블 속성 가방의 배열입니다.|

Table 속성 가방에는 다음 이름/값 쌍이 있습니다.

|이름     |값                               |
|---------|------------------------------------|
|TableName|테이블을 식별하는 문자열입니다. |
|열  |Column 속성 가방의 배열입니다.|
|행     |행 배열의 배열입니다.          |

Column 속성 가방에는 다음과 같은 이름/값 쌍이 있습니다.

|이름      |값                                                          |
|----------|---------------------------------------------------------------|
|ColumnName|열을 식별하는 문자열입니다.                           |
|DataType  |열의 대략적인 .NET 형식을 제공하는 문자열입니다.|
|ColumnType|열의 스칼라 [데이터 형식을](../../query/scalar-data-types/index.md) 제공하는 문자열입니다.|

행 배열은 각 Columns 배열과 동일한 순서를 가지며 관련 열에 대한 행 값이 있는 요소가 하나 있습니다.
JSON(예: `datetime
and `timespan')에서 나타낼 수 없는 스칼라 데이터 형식은 JSON 문자열로 표시됩니다.

다음 예제에서는 형식의 `Table_0` `Text` `string` 단일 열과 단일 행을 보유하는 단일 테이블을 보유하는 경우 이러한 개체가 하나 있습니다.

```json
{
    "Tables": [{
        "TableName": "Table_0",
        "Columns": [{
            "ColumnName": "Text",
            "DataType": "String",
            "ColumnType": "string"
        }],
        "Rows": [["Hello, World!"]]
}
```

또 다른 엑스 메이플 :

![JSON 응답 표현](../images/rest-json-representation.png "rest-json-표현")

## <a name="the-meaning-of-tables-in-the-response"></a>응답에서 테이블의 의미

대부분의 경우 컨트롤 명령은 컨트롤 명령에 의해 생성된 정보를 유지하면서 단일 테이블로 결과를 반환합니다. 예를 들어 `.show databases` 명령은 클러스터의 모든 액세스 가능한 데이터베이스에 대한 세부 정보가 있는 단일 테이블을 반환합니다.

반면 쿼리는 일반적으로 여러 테이블을 반환합니다. 각 [표식 식 문에](../../query/tabularexpressionstatements.md)대해 하나 이상의 테이블이 순서대로 내보내어 명령문에 의해 생성된 결과를 나타냅니다(일괄 [처리](../../query/batches.md) 및 [포크 연산자로](../../query/forkoperator.md)인해 이러한 테이블이 여러 개 있을 수 있음).

또한 일반적으로 세 개의 테이블이 생성됩니다.

* @ExtendedProperties 클라이언트 시각화 지침(예: [렌더 연산자의](../../query/renderoperator.md)정보를 반영하기 위해 내보낸 다) 및 [데이터베이스 커서](../../management/databasecursor.md) 정보와 같은 추가 값을 제공하는 테이블입니다.
* 쿼리 상태 테이블은 쿼리가 성공적으로 완료되었는지 여부 및 쿼리에서 사용한 리소스와 같은 쿼리 자체의 실행에 대한 추가 정보를 제공합니다.
* 마지막으로 내보내지고 결과에 다른 테이블을 나열하는 TableOfContents 테이블입니다.

