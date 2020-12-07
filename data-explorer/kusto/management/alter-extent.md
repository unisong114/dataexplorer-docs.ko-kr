---
title: . 익스텐트 태그 변경-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 alter 익스텐트 명령을 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 07/02/2020
ms.openlocfilehash: 61dba69c3ec40ec13960e9ddf266e47fe88ef3c6
ms.sourcegitcommit: 80f0c8b410fa4ba5ccecd96ae3803ce25db4a442
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/30/2020
ms.locfileid: "96321746"
---
# <a name="alter-extent-tags"></a>. 익스텐트 태그 변경

명령은 특정 데이터베이스의 컨텍스트에서 실행 됩니다. 쿼리에서 반환 하는 모든 익스텐트의 지정 된 [익스텐트 태그](extents-overview.md#extent-tagging) 를 변경 합니다.

변경할 범위 및 태그는 "ExtentId" 라는 열이 포함 된 레코드 집합을 반환 하는 Kusto 쿼리를 사용 하 여 지정 됩니다.

관련 된 모든 테이블에 대 한 [테이블 관리자 권한이](../management/access-control/role-based-authorization.md) 필요 합니다.

> [!NOTE]
> 데이터 분할은 Kusto에서 **익스텐트에서** 호출 되며 모든 명령은 "익스텐트" 또는 "익스텐트"를 동의어로 사용 합니다.
> 익스텐트에 대 한 자세한 내용은 [익스텐트 (데이터 분할) 개요](extents-overview.md)를 참조 하세요.

## <a name="syntax"></a>Syntax

`.alter`[ `async` ] `extent` `tags` `(` '*Tag1*' [ `,` '*Tag2*' `,` ... `,` ' *TagN*'] `)`  <|  *쿼리*

`async` (선택 사항): 비동기적으로 명령을 실행 합니다.
   * 작업 ID (Guid)가 반환 됩니다. 
   * 작업 상태를 모니터링할 수 있습니다. 명령을 사용 [`.show operations`](operations.md#show-operations) 합니다.
   * 성공적으로 실행 된 결과를 검색할 수 있습니다. 명령을 사용 [`.show operation details`](operations.md#show-operation-details) 합니다.

## <a name="restrictions"></a>제한

모든 익스텐트는 컨텍스트 데이터베이스에 있어야 하며 동일한 테이블에 속해야 합니다.

## <a name="return-output"></a>반환 출력

|출력 매개 변수 |형식 |설명|
|---|---|---|
|OriginalExtentId |문자열 |태그가 수정 된 원래 익스텐트의 고유 식별자 (GUID)입니다. 익스텐트는 작업의 일부로 삭제 됩니다.|
|ResultExtentId |문자열 |수정 된 태그를 포함 하는 결과 범위에 대 한 고유 식별자 (GUID)입니다. 익스텐트를 만들어 작업의 일부로 추가 합니다. 오류가 발생 한 경우-"Failed".|
|ResultExtentTags |문자열 |결과 익스텐트가 태그가 지정 된 태그의 컬렉션 이거나, 작업이 실패 하는 경우에는 "null"입니다.|
|세부 정보 |문자열 |작업이 실패 하는 경우 오류 세부 정보를 포함 합니다.|

## <a name="examples"></a>예제

### <a name="alter-tags"></a>태그 변경 

테이블에 있는 모든 익스텐트의 태그를 다음 `MyTable` 으로 변경 `MyTag`

```kusto
.alter extent tags ('MyTag') <| .show table MyTable extents
```

### <a name="alter-tags-of-all-extents"></a>모든 익스텐트의 태그 변경

테이블에서 `MyTable` `drop-by:MyTag` 및로 태그가 지정 된 모든 익스텐트의 태그를 변경 합니다. `drop-by:MyNewTag``MyOtherNewTag`

```kusto
.alter extent tags ('drop-by:MyNewTag','MyOtherNewTag') <| .show table MyTable extents where tags has 'drop-by:MyTag'
```

## <a name="sample-output"></a>샘플 출력

|OriginalExtentId |ResultExtentId | ResultExtentTags | 세부 정보
|---|---|---|---
|e133f050-a1e2-4dad-8552-1f5cf47cab69 |0d96ab2d-9dd2-4d2c-a45e-b24c65aa6687 | drop by: MyNewTag MyOtherNewTag| 
|cdbeb35b-87ea-499f-b545-defbae091b57 |a90a303c-8a14-4207-8f35-d8ea94ca45be | drop by: MyNewTag MyOtherNewTag| 
|4fcb4598-9a31-4614-903c-0c67c286da8c |97aafea1-59ff-4312-b06b-08f42187872f | drop by: MyNewTag MyOtherNewTag| 
|2dfdef64-62a3-4950-a130-96b5b1083b5a |0fb7f3da-5e28-4f09-a000-e62eb41592df | drop by: MyNewTag MyOtherNewTag| 
