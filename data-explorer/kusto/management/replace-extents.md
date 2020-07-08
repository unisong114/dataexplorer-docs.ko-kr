---
title: . 익스텐트 바꾸기-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 익스텐트 바꾸기 명령을 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 07/02/2020
ms.openlocfilehash: cd193cb370136fd7f14a8892f157a895a1d7ad50
ms.sourcegitcommit: d6f35df833d5b4f2829a8924fffac1d0b49ce1c2
ms.contentlocale: ko-KR
ms.lasthandoff: 07/07/2020
ms.locfileid: "86060773"
---
# <a name="replace-extents"></a>. 익스텐트 바꾸기

이 명령은 특정 데이터베이스의 컨텍스트에서 실행 됩니다.
원본 테이블에서 대상 테이블로 지정 된 익스텐트를 이동한 다음 대상 테이블에서 지정 된 범위를 삭제 합니다.
모든 drop 및 move 작업은 단일 트랜잭션에서 수행 됩니다.

원본 및 대상 테이블에 대 한 [테이블 관리자 권한이](../management/access-control/role-based-authorization.md) 필요 합니다.

> [!NOTE]
> 데이터 분할은 Kusto에서 **익스텐트에서** 호출 되며 모든 명령은 "익스텐트" 또는 "익스텐트"를 동의어로 사용 합니다.
> 익스텐트에 대 한 자세한 내용은 [익스텐트 (데이터 분할) 개요](extents-overview.md)를 참조 하세요.

## <a name="syntax"></a>Syntax

`.replace`[ `async` ] `extents` `in` `table` *DestinationTableName* `<| 
{` *query for extents to be dropped from table* `},{` *테이블에 이동할 익스텐트* 를 테이블 쿼리에서 삭제할 익스텐트를 쿼리 합니다.`}`

* `async`(선택 사항): 비동기적으로 명령을 실행 합니다.
    * 작업 ID (Guid)가 반환 됩니다.
    * 작업 상태를 모니터링할 수 있습니다. [. Show operations](operations.md#show-operations) 명령을 사용 합니다.
    * 성공적으로 실행 한 결과를 검색할 수 있습니다. [. 작업 세부 정보 표시](operations.md#show-operation-details) 명령을 사용 합니다.

삭제 하거나 이동할 익스텐트를 지정 하려면 두 쿼리 중 하나를 사용 합니다.
* *테이블에서 삭제할 익스텐트 쿼리*:이 쿼리의 결과는 대상 테이블에서 삭제 해야 하는 익스텐트 id를 지정 합니다.
* *테이블에 이동할 익스텐트 쿼리*:이 쿼리의 결과는 대상 테이블로 이동 해야 하는 원본 테이블의 익스텐트 id를 지정 합니다.

두 쿼리 모두 "ExtentId" 라는 열이 포함 된 레코드 집합을 반환 해야 합니다.

## <a name="restrictions"></a>제한 사항

* 원본 테이블과 대상 테이블은 모두 컨텍스트 데이터베이스에 있어야 합니다.
* *테이블에서 익스텐트를 삭제* 하기 위해 쿼리에서 지정 된 모든 익스텐트는 대상 테이블에 속해야 합니다.
* 원본 테이블의 모든 열은 동일한 이름 및 데이터 형식을 사용 하는 대상 테이블에 있어야 합니다.

## <a name="return-output-for-sync-execution"></a>반환 출력 (동기화 실행의 경우)

출력 매개 변수 |형식 |설명
---|---|---
OriginalExtentId |string |원본 테이블에서 대상 테이블로 이동한 원본 범위의 고유 식별자 (GUID) 또는 삭제 된 대상 테이블의 범위입니다.
ResultExtentId |string |원본 테이블에서 대상 테이블로 이동한 결과 범위에 대 한 고유 식별자 (GUID)입니다. 범위가 대상 테이블에서 삭제 된 경우 비어 있습니다. 실패 시: "Failed"입니다.
설명 |string |작업이 실패 하는 경우 오류 세부 정보를 포함 합니다.

> [!NOTE]
> *테이블 쿼리에서 삭제할 익스텐트가* 반환한 익스텐트가 대상 테이블에 없는 경우 명령이 실패 합니다. 이는 대체 명령이 실행 되기 전에 익스텐트가 병합 된 경우에 발생할 수 있습니다.
> 누락 된 익스텐트의 명령이 실패 하는지 확인 하려면 쿼리가 필요한 ExtentIds를 반환 하는지 확인 합니다. 삭제할 익스텐트가 테이블 *Myothertable*에 없는 경우 아래 예 #1 실패 합니다. 그러나 drop에 대 한 쿼리가 익스텐트 Id를 반환 하지 않았으므로 drop이 존재 하지 않는 경우에도 #2 예제가 성공 합니다.

## <a name="examples"></a>예

### <a name="move-all-extents-from-two-tables"></a>두 테이블에서 모든 익스텐트 이동 

두 개의 특정 테이블 (,)의 모든 익스텐트를 `MyTable1` `MyTable2` 테이블로 이동 `MyOtherTable` 하 고 `MyOtherTable` 태그가 지정 된의 모든 익스텐트를 삭제 합니다 `drop-by:MyTag` .

```kusto
.replace extents in table MyOtherTable <|
    {
        .show table MyOtherTable extents where tags has 'drop-by:MyTag'
    },
    {
        .show tables (MyTable1,MyTable2) extents
    }
```

#### <a name="sample-output"></a>샘플 출력

|OriginalExtentId |ResultExtentId |설명
|---|---|---
|e133f050-a1e2-4dad-8552-1f5cf47cab69 |0d96ab2d-9dd2-4d2c-a45e-b24c65aa6687| 
|cdbeb35b-87ea-499f-b545-defbae091b57 |a90a303c-8a14-4207-8f35-d8ea94ca45be| 
|4fcb4598-9a31-4614-903c-0c67c286da8c |97aafea1-59ff-4312-b06b-08f42187872f| 
|2dfdef64-62a3-4950-a130-96b5b1083b5a |0fb7f3da-5e28-4f09-a000-e62eb41592df| 

### <a name="move-all-extents-from-one-table-to-another-drop-specific-extent"></a>한 테이블에서 다른 테이블로 모든 익스텐트 이동, 특정 익스텐트 삭제

특정 tavle ()의 모든 익스텐트를 `MyTable1` 테이블로 이동 `MyOtherTable` 하 고 해당 ID로의 특정 익스텐트를 삭제 합니다 `MyOtherTable` .

```kusto
.replace extents in table MyOtherTable <|
    {
        print ExtentId = "2cca5844-8f0d-454e-bdad-299e978be5df"
    },
    {
        .show table MyTable1 extents 
    }
```

```kusto
.replace extents in table MyOtherTable  <|
    {
        .show table MyOtherTable extents
        | where ExtentId == guid(2cca5844-8f0d-454e-bdad-299e978be5df) 
    },
    {
        .show table MyTable1 extents 
    }
```

### <a name="implement-an-idempotent-logic"></a>Idempotent 논리 구현

`t_dest`테이블에서 테이블로 이동할 익스텐트가 있는 경우 Kusto가 테이블에서 익스텐트를 삭제 하도록 idempotent 논리를 구현 합니다 `t_source` `t_dest` .

```kusto
.replace async extents in table t_dest <|
{
    let any_extents_to_move = toscalar( 
        t_source
        | where extent_tags() has 'drop-by:blue'
        | summarize count() > 0
    );
    let extents_to_drop =
        t_dest
        | where any_extents_to_move and extent_tags() has 'drop-by:blue'
        | summarize by ExtentId = extent_id()
    ;
    extents_to_drop
},
{
    let extents_to_move = 
        t_source
        | where extent_tags() has 'drop-by:blue'
        | summarize by ExtentId = extent_id()
    ;
    extents_to_move
}
```
