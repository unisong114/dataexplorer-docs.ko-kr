---
title: 쿼리/관리 HTTP 응답-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 쿼리/관리 HTTP 응답을 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 11/05/2018
ms.openlocfilehash: 2642ffc6b87afab785dc5f7ba962e1f659232cc2
ms.sourcegitcommit: 7fa9d0eb3556c55475c95da1f96801e8a0aa6b0f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2020
ms.locfileid: "91942270"
---
# <a name="querymanagement-http-response"></a>쿼리/관리 HTTP 응답

## <a name="response-status"></a>응답 상태

Http 응답 상태 줄은 HTTP 표준 응답 코드를 따릅니다.
예를 들어 코드 200은 성공을 나타냅니다. 

다음 상태 코드는 현재 사용 중입니다. 유효한 HTTP 코드가 반환 될 수도 있습니다.

|코드|하위        |Description                                    |
|----|---------------|-----------------------------------------------|
|100 |계속       |클라이언트는 요청을 계속 해 서 보낼 수 있습니다.       |
|200 |확인             |요청이 처리를 시작 했습니다.       |
|400 |BadRequest     |요청의 형식이 잘못 되었고 (영구적) 실패 했습니다.|
|401 |권한 없음   |클라이언트는 먼저 인증 해야 합니다.            |
|403 |사용할 수 없음      |클라이언트 요청이 거부 되었습니다.                      |
|404 |NotFound       |요청에서 존재 하지 않는 엔터티를 참조 합니다.      |
|413 |PayloadTooLarge|요청 페이로드가 제한을 초과 했습니다.               |
|429 |TooManyRequests|제한으로 인해 요청이 거부 되었습니다. |
|504 |제한 시간        |요청 시간이 초과 되었습니다.                         |
|520 |ServiceError   |서비스에서 요청을 처리 하는 동안 오류가 발생 했습니다.|

> [!NOTE]
> 200 상태 코드는 요청 처리가 성공적으로 시작 되었음을 보여 주고 성공적으로 완료 된 것은 아닙니다.
> 200 상태 코드가 반환 된 후 요청을 처리 하는 동안 발생 한 오류는 "부분 쿼리 오류" 라고 하며, 발생 하면 클라이언트에 게 경고를 표시 하기 위해 특별 표시기가 응답 스트림에 삽입 됩니다.

## <a name="response-headers"></a>응답 헤더

다음 사용자 지정 헤더가 반환 됩니다.

|사용자 지정 헤더           |Description                                                                                               |
|------------------------|----------------------------------------------------------------------------------------------------------|
|`x-ms-client-request-id`|동일한 이름의 요청 헤더 또는 일부 고유 식별자에 전송 된 고유한 요청 식별자입니다.     |
|`x-ms-activity-id`      |요청에 대 한 전역적으로 고유한 상관 관계 식별자입니다. 서비스에 의해 생성 됩니다.                    |

## <a name="response-body"></a>응답 본문

상태 코드가 200 인 경우 응답 본문은 쿼리 또는 제어 명령의 결과를 사각형 테이블 시퀀스로 인코딩하는 JSON 문서입니다.
자세한 내용은 다음을 참조하십시오.

> [!NOTE]
> 테이블 시퀀스는 SDK에 의해 반영 됩니다. 예를 들어 .NET Framework Kusto. 데이터 라이브러리를 사용 하는 경우 테이블의 시퀀스는 SDK에서 반환 되는 개체의 결과가 됩니다 `System.Data.IDataReader` .

상태 코드에서 401이 아닌 4xx 또는 5xx 오류를 나타내는 경우 응답 본문은 실패의 세부 정보를 인코딩하는 JSON 문서입니다.
자세한 내용은 [Microsoft REST API 지침](https://github.com/microsoft/api-guidelines)을 참조 하세요.

> [!NOTE]
> `Accept`헤더가 요청에 포함 되지 않은 경우 실패의 응답 본문은 JSON 문서가 아닐 수 있습니다.

## <a name="json-encoding-of-a-sequence-of-tables"></a>테이블 시퀀스의 JSON 인코딩

테이블 시퀀스의 JSON 인코딩은 다음 이름/값 쌍을 포함 하는 단일 JSON 속성 모음입니다.

|Name  |값                              |
|------|-----------------------------------|
|테이블|테이블 속성 모음에 대 한 배열입니다.|

테이블 속성 모음에는 다음과 같은 이름/값 쌍이 있습니다.

|Name     |값                               |
|---------|------------------------------------|
|TableName|테이블을 식별 하는 문자열입니다. |
|열  |열 속성 모음에 대 한 배열입니다.|
|행     |행 배열의 배열입니다.          |

열 속성 모음에는 다음과 같은 이름/값 쌍이 있습니다.

|Name      |값                                                          |
|----------|---------------------------------------------------------------|
|ColumnName|열을 식별하는 문자열입니다.                           |
|DataType  |열의 대략적인 .NET 형식을 제공 하는 문자열입니다.|
|ColumnType|열의 [스칼라 데이터 형식을](../../query/scalar-data-types/index.md) 제공 하는 문자열입니다.|

행 배열의 순서는 해당 열 배열과 동일 합니다.
또한 행 배열에는 관련 열에 대 한 행의 값과 일치 하는 요소가 하나 있습니다.
및와 같이 JSON으로 표현할 수 없는 스칼라 데이터 형식은 `datetime` `timespan` json 문자열로 표시 됩니다.

다음 예에서는 `Table_0` 형식의 단일 열 `Text` 과 단일 행을 포함 하는 라는 단일 테이블을 포함 하는 경우 가능한 개체를 보여 줍니다 `string` .

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

다음은 또 다른 예제입니다. 

:::image type="content" source="../images/rest-json-representation.png" alt-text="테이블 개체의 배열을 포함 하는 JSON 파일의 트리 뷰를 보여 주는 스크린샷":::

## <a name="the-meaning-of-tables-in-the-response"></a>응답에 있는 테이블의 의미

대부분의 경우 제어 명령은 제어 명령에 의해 생성 된 정보를 포함 하는 단일 테이블을 포함 하는 결과를 반환 합니다. 예를 들어이 `.show databases` 명령은 클러스터의 모든 액세스 가능한 데이터베이스에 대 한 세부 정보가 포함 된 단일 테이블을 반환 합니다.

쿼리는 일반적으로 여러 테이블을 반환 합니다.
각 [테이블 형식 식 문에](../../query/tabularexpressionstatements.md)대해 하나 이상의 테이블이 순서 대로 생성 되어 문에 의해 생성 된 결과를 나타냅니다.

> [!NOTE]
> [일괄 처리](../../query/batches.md) 및 [포크 연산자로](../../query/forkoperator.md)인해 이러한 테이블이 여러 개 있을 수 있습니다.

세 개의 테이블이 생성 되는 경우가 많습니다.
* @ExtendedProperties클라이언트 시각화 지침과 같은 추가 값을 제공 하는 테이블입니다. 예를 들어 [render 연산자](../../query/renderoperator.md)의 정보를 반영 하기 위해 이러한 값이 생성 되 고 [데이터베이스 커서가](../../management/databasecursor.md)생성 됩니다.
  
  이 테이블에는 `string` JSON과 유사한 값을 포함 하는 형식의 단일 열이 있습니다.

  |값|
  |-----|
  |{"시각화": "piechart",...}|
  |{"Cursor": "637239957206013576"}|

* 쿼리 자체의 실행에 대 한 추가 정보 (예: 성공적으로 완료 된 경우) 및 쿼리에서 사용 된 리소스를 제공 하는 QueryStatus 테이블입니다.

  이 테이블의 구조는 다음과 같습니다.

  |타임스탬프                  |심각도|SeverityName|StatusCode|StatusDescription            |개수|RequestId|ActivityId|SubActivityId|ClientActivityId|
  |---------------------------|--------|------------|----------|-----------------------------|-----|---------|----------|-------------|----------------|
  |2020-05-02 06:09:12.7052077|4       |정보        | 0        | 쿼리가 완료 되었습니다.|1    |...      |...       |...          |...             |

  심각도 값이 2 이상인 경우 실패를 의미 합니다.

* 마지막으로 만들어진 TableOfContents 테이블은 결과의 다른 테이블을 나열 합니다. 

  이 테이블에 대 한 예는 다음과 같습니다.

  |서수|종류            |name               |Id                                  |PrettyName|
  |-------|----------------|-------------------|------------------------------------|----------|
  |0      | QueryResult    |PrimaryResult      |db9520f9-0455-4cb5-b257-53068497605a||
  |1      | QueryProperties|@ExtendedProperties|908901f6-5319-4809-ae9e-009068c267c7||
  |2      | QueryStatus    |QueryStatus        |00000000-0000-0000-0000-000000000000||
