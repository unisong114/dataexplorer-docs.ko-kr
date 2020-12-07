---
title: Alter 구체화 된 뷰-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 구체화 된 뷰를 변경 하는 방법을 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: yifats
ms.service: data-explorer
ms.topic: reference
ms.date: 08/30/2020
ms.openlocfilehash: 03d047d89abc3de3c80414564ec101f309bf69b0
ms.sourcegitcommit: 80f0c8b410fa4ba5ccecd96ae3803ce25db4a442
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/30/2020
ms.locfileid: "96320573"
---
# <a name="alter-materialized-view"></a>.alter materialized-view

구체화 된 [뷰를](materialized-view-overview.md) 변경 하는 것은 뷰에서 기존 데이터를 유지 하면서 구체화 된 뷰의 쿼리를 변경 하는 데 사용할 수 있습니다.

[데이터베이스 관리자](../access-control/role-based-authorization.md) 권한 또는 구체화 된 뷰의 관리자가 필요 합니다.

> [!WARNING]
> 구체화 된 뷰를 변경 하는 경우 각별히 주의 해야 합니다. 잘못 된 사용으로 인해 데이터가 손실 될 수 있습니다.

## <a name="syntax"></a>Syntax

`.alter` `materialized-view`  
[ `with` `(` *PropertyName* `=` *PropertyValue* `,` ... `)` ]  
*ViewName* `on table` *Sourcetablename*  
`{`  
    &nbsp;&nbsp;&nbsp;&nbsp;*쿼리입니다*  
`}`

## <a name="arguments"></a>인수

|인수|형식|설명
|----------------|-------|---|
|ViewName|String|구체화 된 뷰 이름입니다.|
|(|String|뷰가 정의 된 원본 테이블의 이름입니다.|
|쿼리|String|구체화 된 뷰 쿼리입니다.|

## <a name="properties"></a>속성

는 `dimensionTables` 구체화 된 alter view 명령에서 유일 하 게 지원 되는 속성입니다. 이 속성은 쿼리에서 차원 테이블을 참조 하는 경우에 사용 해야 합니다. 자세한 내용은 명령을 참조 하세요 [`.create materialized-view`](materialized-view-create.md) .

## <a name="use-cases"></a>사용 사례

* 뷰에 집계를 추가 합니다. 예를 들어 `avg` 뷰 쿼리를로 변경 하 여 집계를에 추가 `T | summarize count(), min(Value) by Id` `T | summarize count(), min(Value), avg(Value) by Id` 합니다.
* 요약 연산자 이외의 연산자를 변경 합니다. 예를 들어를로 변경 하 여 일부 레코드를 필터링  `T | summarize arg_max(Timestamp, *) by User` `T | where User != 'someone' | summarize arg_max(Timestamp, *) by User` 합니다.
* 원본 테이블의 변경으로 인해 쿼리를 변경 하지 않고 변경 합니다. 예를 들어 `T | summarize arg_max(Timestamp, *) by Id` , `autoUpdateSchema` (명령 참조)로 설정 되지 않은 뷰가 있다고 가정 [`.create materialized-view`](materialized-view-create.md) 합니다. 뷰의 원본 테이블에서 열을 추가 하거나 제거 하면 뷰가 자동으로 사용 하지 않도록 설정 됩니다. 정확히 동일한 쿼리로 alter 명령을 실행 하 여 구체화 된 뷰의 스키마를 새 테이블 스키마에 맞게 변경 합니다. 뷰는 [구체화 된 뷰 사용](materialized-view-enable-disable.md) 명령을 사용 하 여 변경 후에도 명시적으로 사용 하도록 설정 해야 합니다.

## <a name="alter-materialized-view-limitations"></a>구체화 된 뷰 제한 변경

* **지원 되지 않는 변경 내용:**
    * 열 유형 변경은 지원 되지 않습니다.
    * 열 이름 바꾸기는 지원 되지 않습니다. 예를 들어의 뷰를로 변경 하면 `T | summarize count() by Id` `T | summarize Count=count() by Id` 열이 삭제 되 `count_` 고 새 열이 생성 됩니다 .이 열에는 `Count` 처음에 null만 포함 됩니다.
    * 구체화 된 뷰 그룹을 식으로 변경 하는 것은 지원 되지 않습니다.

* **기존 데이터에 미치는 영향:**
    * 구체화 된 뷰를 변경 해도 기존 데이터에는 영향을 주지 않습니다.
    * 새 열은 레코드가 수집 게시 될 때까지 모든 기존 레코드에 대해 null 값을 받습니다.
        * 예를 들어 보기가 `T | summarize count() by bin(Timestamp, 1d)` 로 변경 된 경우 뷰를 `T | summarize count(), sum(Value) by bin(Timestamp, 1d)` `Timestamp=T` 변경 하기 전에 레코드가 이미 처리 된 특정에 대해 `sum` 열에 부분 데이터가 포함 됩니다. 이 보기에는 alter execution 후에 처리 된 레코드만 포함 됩니다.
    * 쿼리에 필터를 추가 해도 이미 구체화 된 레코드는 변경 되지 않습니다. 필터는 새로 수집 레코드에만 적용 됩니다.
