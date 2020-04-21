---
title: 쿼리 V2 HTTP 응답 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기에서 쿼리 V2 HTTP 응답을 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/11/2020
ms.openlocfilehash: cca9b8381c7c59993c1e9071c46f34c1754d2429
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81524313"
---
# <a name="query-v2-http-response"></a>V2 HTTP 응답 쿼리

상태 코드가 200이면 응답 본문이 JSON 배열입니다.
배열의 각 JSON 개체를 _프레임이라고_합니다.

프레임에는 7가지 유형이 있습니다.

1. 데이터 셋헤더
2. 테이블 헤더
3. 테이블 프래그 ()조각
4. 테이블 진행률
5. 테이블 완성
6. DataTable
7. 데이터 집합 완료

## <a name="datasetheader"></a>데이터 셋헤더 

이것은 항상 데이터 집합의 첫 번째 프레임이며 정확히 한 번 나타납니다.

```json
{
    "Version": string,
    "IsProgressive": Boolean
}
```

위치:

1. `Version`프로토콜 버전을 보유합니다. 현재 버전은 `v2.0`입니다.
2. `IsProgressive`는 이 데이터 집합에 프로그레시브 프레임이 포함되어 있는지 여부를 나타내는 부울 플래그입니다. 프로그레시브 프레임은 다음 중 하나입니다.
    1. `TableHeader`: 표에 대한 일반 정보 포함
    2. `TableFragment`: 테이블의 직사각형 데이터 샤드 포함
    3. `TableProgress`: 진행률(0-100)을 포함합니다.
    4. `TableCompletion`: 테이블의 마지막 프레임임을 표시합니다.
        
    위의 네 프레임은 테이블을 설명하기 위해 함께 사용됩니다.
    이 플래그가 설정되지 않으면 집합의 모든 테이블이 단일 프레임을 사용하여 직렬화됩니다.
      1. `DataTable`: 데이터 집합의 단일 테이블에 대해 클라이언트가 필요로 하는 모든 정보를 포함합니다.


## <a name="tableheader"></a>테이블 헤더

`results_progressive_enabled` 옵션을 true로 설정한 쿼리에는 이 프레임이 포함될 수 있습니다. 이 테이블 다음에 클라이언트는 `TableFragment` `TableProgress` 프레임과 프레임의 `TableCompletion` 인터리빙 시퀀스를 예상해야 합니다. 그런 다음 해당 테이블과 관련된 프레임이 더 이상 전송되지 않습니다.

```json
{
    "TableId": Number,
    "TableKind": string,
    "TableName": string,
    "Columns": Array,
}
```

위치:

1. `TableId`은 테이블의 고유 ID입니다.
2. `TableKind`는 다음 중 하나가 될 수 있는 테이블의 종류입니다.

      * 기본 결과
      * 쿼리 완료정보
      * 쿼리 추적 로그
      * 쿼리퍼로그
      * TableOfContents
      * QueryProperties
      * 쿼리 계획
      * 알 수 없음
3. `TableName`은 테이블의 이름입니다.
4. `Columns`는 테이블의 스키마를 설명하는 배열입니다.

```json
{
    "ColumnName": string,
    "ColumnType": string,
}
```
지원되는 열 유형은 [여기에](../../query/scalar-data-types/index.md)설명되어 있습니다.

## <a name="tablefragment"></a>테이블 프래그 ()조각

이 프레임에는 테이블의 직사각형 데이터 조각이 포함되어 있습니다. 실제 데이터 외에도 이 프레임에는 `TableFragmentType` 클라이언트에게 조각으로 수행할 작업을 알려주는 속성이 포함되어 있습니다(기존 조각에 추가되거나 모두 함께 대체할 수 있음).

```json
{
    "TableId": Number,
    "FieldCount": Number,
    "TableFragmentType": string,
    "Rows": Array
}
```

위치:

1. `TableId`은 테이블의 고유 ID입니다.
2. `FieldCount`은 테이블의 열 수입니다.
3. `TableFragmentType`은 클라이언트가 이 조각으로 수행해야 하는 작업을 설명합니다. 다음 중 하나일 수 있습니다.
      * 데이터 앱
      * 데이터 대체
4. `Rows`는 조각 데이터를 포함하는 2차원 배열입니다.

## <a name="tableprogress"></a>테이블 진행률

이 프레임은 위에서 설명한 `TableFragment` 프레임과 인터리브할 수 있습니다.
유일한 목적은 쿼리 진행률에 대해 클라이언트에게 알리는 것입니다.

```json
{
    "TableId": Number,
    "TableProgress": Number,
}
```

위치:

1. `TableId`은 테이블의 고유 ID입니다.
2. `TableProgress`백분율(0-100)의 진행률입니다.

## <a name="tablecompletion"></a>테이블 완성

프레임은 `TableCompletion` 테이블 전송의 끝을 표시합니다. 해당 테이블과 관련된 프레임은 더 이상 전송되지 않습니다.

```json
{
    "TableId": Number,
    "RowCount": Number,
}
```    

위치:

1. `TableId`은 테이블의 고유 ID입니다.
2. `RowCount`는 테이블의 마지막 행 수입니다.

## <a name="datatable"></a>DataTable

`EnableProgressiveQuery` false로 설정된 플래그로 발급된 쿼리에는`TableHeader`이전 `TableFragment` `TableProgress` `TableCompletion`4프레임(및)이 포함되지 않습니다. 대신 데이터 집합의 각 테이블은 테이블을 읽기 위해 `DataTable` 클라이언트가 필요로 하는 모든 정보를 포함하는 단일 프레임인 프레임을 사용하여 전송됩니다.

```json
{
    "TableId": Number,

    "TableKind": string,

    "TableName": string,

    "Columns": Array,

    "Rows": Array,
}
```    

위치:

1. `TableId`은 테이블의 고유 ID입니다.
2. `TableKind`는 다음 중 하나가 될 수 있는 테이블의 종류입니다.

      * 기본 결과
      * 쿼리 완료정보
      * 쿼리 추적 로그
      * 쿼리퍼로그
      * QueryProperties
      * 쿼리 계획
      * 알 수 없음
3. `TableName`은 테이블의 이름입니다.
4. `Columns`는 테이블의 스키마를 설명하는 배열입니다.

```json
{
    "ColumnName": string,
    "ColumnType": string,
}
```
4. `Rows`는 테이블의 데이터를 포함하는 2차원 배열입니다.

### <a name="the-meaning-of-tables-in-the-response"></a>응답에서 테이블의 의미

* `PrimaryResult`- 쿼리의 기본 테이블 형식 결과입니다. 각 [표식 식 문에](../../query/tabularexpressionstatements.md)대해 하나 이상의 테이블이 순서대로 내보내어 명령문에 의해 생성된 결과를 나타냅니다(일괄 [처리](../../query/batches.md) 및 [포크 연산자로](../../query/forkoperator.md)인해 이러한 테이블이 여러 개 있을 수 있음).
* `QueryCompletionInformation`- 쿼리가 성공적으로 완료되었는지 여부 및 쿼리에서 사용한 리소스(v1 응답의 QueryStatus 테이블과 유사)와 같은 쿼리 자체의 실행에 대한 추가 정보를 제공합니다. 
* `QueryProperties`- 클라이언트 시각화 지침(예: [렌더 연산자의](../../query/renderoperator.md)정보를 반영하기 위해 내보낸 다) 및 [데이터베이스 커서](../../management/databasecursor.md) 정보와 같은 추가 값을 제공합니다.
* `QueryTraceLog`- 성능 추적 로그 정보(클라이언트 [요청 속성에서](../netfx/request-properties.md)perftrace를 설정할 때 반환됨).

## <a name="datasetcompletion"></a>데이터 집합 완료

데이터 집합의 마지막 프레임입니다.
```json
{
    "HasErrors": Boolean,
    "Cancelled": Boolean,
    "OneApiErrors": Array,
}
```

위치:

1. `HasErrors`데이터 집합을 생성하는 오류가 있는 경우 true입니다.
2. `Cancelled`데이터 집합의 생성으로 이어지는 요청이 중간에 취소된 경우 해당됩니다. 
3. `OneApiErrors`true인 경우에만 `HasErrors` 전송됩니다. 형식에 `OneApiErrors` 대한 설명은 [여기에서](https://github.com/Microsoft/api-guidelines/blob/vNext/Guidelines.md)섹션 7.10.2를 참조하십시오.