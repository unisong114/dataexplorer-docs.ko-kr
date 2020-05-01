---
title: V2 HTTP 응답 쿼리-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 쿼리 V2 HTTP 응답을 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/11/2020
ms.openlocfilehash: 86a56d77005b2c6b5c9d38bbec85eebfbcb481dc
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/01/2020
ms.locfileid: "82617904"
---
# <a name="query-v2-http-response"></a>V2 HTTP 응답 쿼리

상태 코드가 200 인 경우 응답 본문은 JSON 배열입니다.
배열의 각 JSON 개체를 _프레임_이라고 합니다.

프레임에는 다음과 같은 여러 가지 유형이 있습니다.

* [DataSetHeader](#datasetheader)
* [TableHeader](#tableheader)
* [TableFragment](#tablefragment)
* [TableProgress](#tableprogress)
* [TableCompletion](#tablecompletion)
* [DataTable](#datatable)
* [DataSetCompletion](#datasetcompletion)

## <a name="datasetheader"></a>DataSetHeader 

`DataSetHeader` 프레임은 항상 데이터 집합의 첫 번째 이며 한 번만 나타납니다.

```json
{
    "Version": string,
    "IsProgressive": Boolean
}
```

위치:

* `Version`프로토콜 버전입니다. 현재 버전은 `v2.0`입니다.
* `IsProgressive`이 데이터 집합에 프로그레시브 프레임이 포함 되어 있는지 여부를 나타내는 부울 플래그입니다. 
   프로그레시브 프레임은 다음 중 하나입니다.
   
     | 프레임             | 설명                                    |
     |-------------------| -----------------------------------------------|
     | `TableHeader`     | 테이블에 대 한 일반 정보를 포함 합니다.   |
     | `TableFragment`   | 테이블의 사각형 데이터 분할 영역을 포함 합니다. |
     | `TableProgress`   | 백분율 (0-100)의 진행률을 포함 합니다.       |
     | `TableCompletion` | 이 프레임이 마지막 프레임 임을 나타냅니다.      |
        
    위의 프레임은 테이블을 설명 합니다.
    `IsProgressive` 플래그가 true로 설정 되어 있지 않으면 집합의 모든 테이블이 단일 프레임을 사용 하 여 serialize 됩니다.
* `DataTable`: 클라이언트에서 데이터 집합의 단일 테이블에 대해 필요로 하는 모든 정보를 포함 합니다.

## <a name="tableheader"></a>TableHeader

`results_progressive_enabled` 옵션을 true로 설정 하 여 만든 쿼리는이 프레임을 포함할 수 있습니다. 이 표 다음에 나오는 클라이언트는 및 `TableFragment` `TableProgress` 프레임의 인터리브 시퀀스를 예측할 수 있습니다. 테이블의 마지막 프레임은 `TableCompletion`입니다.

```json
{
    "TableId": Number,
    "TableKind": string,
    "TableName": string,
    "Columns": Array,
}
```

위치:

* `TableId`테이블의 고유 ID입니다.
* `TableKind`다음 중 하나입니다.

    * PrimaryResult
    * QueryCompletionInformation
    * QueryTraceLog
    * QueryPerfLog
    * TableOfContents
    * QueryProperties
    * QueryPlan
    * Unknown
      
* `TableName`테이블의 이름입니다.
* `Columns`는 테이블의 스키마를 설명 하는 배열입니다.

```json
{
    "ColumnName": string,
    "ColumnType": string,
}
```

지원 되는 열 유형은 [여기](../../query/scalar-data-types/index.md)에 설명 되어 있습니다.

## <a name="tablefragment"></a>TableFragment

프레임 `TableFragment` 에는 테이블의 사각형 데이터 조각이 포함 됩니다. 실제 데이터 외에도이 프레임에는 조각으로 수행할 `TableFragmentType` 작업을 클라이언트에 알리는 속성도 포함 되어 있습니다. 기존 조각에 추가 된 조각 또는 대체 합니다.

```json
{
    "TableId": Number,
    "FieldCount": Number,
    "TableFragmentType": string,
    "Rows": Array
}
```

위치:

* `TableId`테이블의 고유 ID입니다.
* `FieldCount`테이블의 열 수입니다.
* `TableFragmentType`클라이언트에서이 조각으로 수행 해야 하는 작업을 설명 합니다. 
    `TableFragmentType`다음 중 하나입니다.
    
    * DataAppend
    * DataReplace
      
* `Rows`는 조각 데이터를 포함 하는 2 차원 배열입니다.

## <a name="tableprogress"></a>TableProgress

프레임 `TableProgress` 은 위에서 설명한 `TableFragment` 프레임을 사용 하 여 인터리브 할 수 있습니다.
유일한 용도는 클라이언트에 쿼리 진행 상황을 알리는 것입니다.

```json
{
    "TableId": Number,
    "TableProgress": Number,
}
```

위치:

* `TableId`테이블의 고유 ID입니다.
* `TableProgress`백분율 (0--100)의 진행률입니다.

## <a name="tablecompletion"></a>TableCompletion

프레임 `TableCompletion` 은 테이블 전송의 끝을 표시 합니다. 해당 테이블과 관련 된 더 이상 프레임이 전송 되지 않습니다.

```json
{
    "TableId": Number,
    "RowCount": Number,
}
```    

위치:

* `TableId`테이블의 고유 ID입니다.
* `RowCount`테이블의 총 행 수입니다.

## <a name="datatable"></a>DataTable

False `EnableProgressiveQuery` 로 설정 된 플래그를 사용 하 여 실행 되는 쿼리에는 프레임 (`TableHeader`, `TableFragment` `TableProgress`, 및 `TableCompletion`)이 포함 되지 않습니다. 대신 테이블을 읽기 위해 클라이언트에 필요한 모든 정보가 포함 된 `DataTable` 프레임을 사용 하 여 데이터 집합의 각 테이블이 전송 됩니다.

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

* `TableId`테이블의 고유 ID입니다.
* `TableKind`다음 중 하나입니다.

    * PrimaryResult
    * QueryCompletionInformation
    * QueryTraceLog
    * QueryPerfLog
    * QueryProperties
    * QueryPlan
    * Unknown
      
* `TableName`테이블의 이름입니다.
* `Columns`는 테이블의 스키마를 설명 하 고 다음을 포함 하는 배열입니다.

```json
{
    "ColumnName": string,
    "ColumnType": string,
}
```

* `Rows`는 테이블의 데이터를 포함 하는 2 차원 배열입니다.

### <a name="the-meaning-of-tables-in-the-response"></a>응답에 있는 테이블의 의미

* `PrimaryResult`-쿼리의 기본 표 형식 결과입니다. 각 [테이블 형식 식 문에](../../query/tabularexpressionstatements.md)대해 하나 이상의 테이블이 순서 대로 생성 되어 문에 의해 생성 된 결과를 나타냅니다. [일괄 처리](../../query/batches.md) 및 [포크 연산자로](../../query/forkoperator.md)인해 이러한 테이블이 여러 개 있을 수 있습니다.
* `QueryCompletionInformation`-쿼리 자체의 실행에 대 한 추가 정보를 제공 합니다 (예: 성공적으로 완료 되었는지 여부 및 쿼리에서 사용 되는 리소스 (v1 응답의 QueryStatus 테이블과 비슷함). 
* `QueryProperties`-클라이언트 시각화 지침 (예: [render 연산자](../../query/renderoperator.md)의 정보를 반영 하기 위해 내보내기) 및 [데이터베이스 커서](../../management/databasecursor.md) 정보 등의 추가 값을 제공 합니다.
* `QueryTraceLog`-성능 추적 로그 정보 ( `perftrace` [클라이언트 요청 속성](../netfx/request-properties.md) 에서가 true로 설정 된 경우 반환 됨)

## <a name="datasetcompletion"></a>DataSetCompletion

`DataSetCompletion` 프레임은 데이터 집합의 마지막 프레임입니다.

```json
{
    "HasErrors": Boolean,
    "Cancelled": Boolean,
    "OneApiErrors": Array,
}
```

위치:

* `HasErrors`데이터 집합을 생성 하는 동안 오류가 발생 한 경우 true입니다.
* `Cancelled`데이터 집합의 생성을 초래한 요청이 완료 되기 전에 취소 된 경우 true입니다. 
* `OneApiErrors`이 true 인 경우 `HasErrors` 에만이 반환 됩니다. `OneApiErrors` 형식에 대 한 설명은 [여기](https://github.com/Microsoft/api-guidelines/blob/vNext/Guidelines.md)7.10.2 섹션을 참조 하세요.
