---
title: . 익스텐트 태그를 삭제 합니다.-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 drop 익스텐트의 tags 명령을 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 07/02/2020
ms.openlocfilehash: 23784a1e3e00c242665a43dffcc528bfeff68896
ms.sourcegitcommit: d6f35df833d5b4f2829a8924fffac1d0b49ce1c2
ms.contentlocale: ko-KR
ms.lasthandoff: 07/07/2020
ms.locfileid: "86060696"
---
# <a name="drop-extent-tags"></a>. 익스텐트 태그를 삭제 합니다.

명령은 특정 데이터베이스의 컨텍스트에서 실행 됩니다. 데이터베이스 및 테이블의 모든 또는 특정 익스텐트의 특정 [익스텐트 태그](extents-overview.md#extent-tagging) 를 삭제 합니다.  

> [!NOTE]
> 데이터 분할은 Kusto에서 **익스텐트에서** 호출 되며 모든 명령은 "익스텐트" 또는 "익스텐트"를 동의어로 사용 합니다.
> 익스텐트에 대 한 자세한 내용은 [익스텐트 (데이터 분할) 개요](extents-overview.md)를 참조 하세요.

다음 두 가지 방법으로 익스텐트를 제거할 태그를 지정할 수 있습니다.

* 지정 된 테이블의 모든 범위에서 제거 해야 하는 태그를 명시적으로 지정 합니다.
* 결과에서 테이블의 익스텐트 Id를 지정 하는 쿼리 및 각 익스텐트 (제거 해야 하는 태그)를 제공 합니다.

## <a name="syntax"></a>Syntax

`.drop`[ `async` ] `extent` `tags` `from` `table` *TableName* `(` '*Tag1*' [ `,` '*Tag2*' `,` ... `,` ' *TagN*']`)`

`.drop`[ `async` ] `extent` `tags`  <|  *쿼리*

`async`(선택 사항): 비동기적으로 명령을 실행 합니다.
   * 작업 ID (Guid)가 반환 됩니다.
   * 작업 상태를 모니터링할 수 있습니다. [. Show operations](operations.md#show-operations) 명령을 사용 합니다.
   * [. 작업 세부 정보 표시](operations.md#show-operation-details) 명령을 사용 하 여 성공적으로 실행 한 결과를 검색 합니다.

## <a name="restrictions"></a>제한 사항

모든 익스텐트는 컨텍스트 데이터베이스에 있어야 하며 동일한 테이블에 속해야 합니다.

## <a name="specify-extents-with-a-query"></a>쿼리를 사용 하 여 익스텐트 지정

삭제할 범위 및 태그는 Kusto 쿼리를 사용 하 여 지정 됩니다. "ExtentId" 열과 "Tags" 라는 열이 포함 된 레코드 집합을 반환 합니다.

> [!NOTE]
> [Kusto .net 클라이언트 라이브러리](../api/netfx/about-kusto-data.md)를 사용 하는 경우 다음 메서드는 필수 명령을 생성 합니다.
> * `CslCommandGenerator.GenerateExtentTagsDropByRegexCommand(string tableName, string regex)`
> * `CslCommandGenerator.GenerateExtentTagsDropBySubstringCommand(string tableName, string substring)`

관련 된 모든 원본 및 대상 테이블에 대해 [테이블 관리자 권한이](../management/access-control/role-based-authorization.md) 필요 합니다.

### <a name="syntax-for-drop-extent-tags-in-query"></a>. 쿼리의 drop 익스텐트 태그

```kusto 
.drop extent tags <| ...query...
```

### <a name="return-output"></a>반환 출력

출력 매개 변수 |형식 |설명 
---|---|---
OriginalExtentId |string |태그가 수정 된 원래 익스텐트의 고유 식별자 (GUID)입니다. 익스텐트는 작업의 일부로 삭제 됩니다.
ResultExtentId |string |수정 된 태그를 포함 하는 결과 범위에 대 한 고유 식별자 (GUID)입니다. 익스텐트를 만들어 작업의 일부로 추가 합니다. 오류가 발생 한 경우-"Failed".
ResultExtentTags |string |결과 범위에 태그가 지정 된 태그의 컬렉션 (있는 경우) 이거나, 작업이 실패 하는 경우에는 "null"입니다.
설명 |string |작업이 실패 하는 경우 오류 세부 정보를 포함 합니다.

## <a name="examples"></a>예

### <a name="drop-one-tag"></a>태그 하나를 삭제 합니다.

태그가 `drop-by:Partition000` 지정 된 테이블의 모든 범위에서 태그를 삭제 합니다.

```kusto
.drop extent tags from table MyOtherTable ('drop-by:Partition000')
```

### <a name="drop-several-tags"></a>여러 태그 삭제

다음 중 하나를 `drop-by:20160810104500` `a random tag` 사용 하 `drop-by:20160810` 여 태그가 지정 된 테이블의 모든 범위에서, 및 태그를 삭제 합니다.

```kusto
.drop extent tags from table [My Table] ('drop-by:20160810104500','a random tag','drop-by:20160810')
```

### <a name="drop-all-drop-by-tags"></a>모든 `drop-by` 태그 삭제 

`drop-by`테이블에서 익스텐트의 모든 태그를 삭제 합니다 `MyTable` .

```kusto
.drop extent tags <| 
  .show table MyTable extents 
  | where isnotempty(Tags)
  | extend Tags = split(Tags, '\r\n') 
  | mv-expand Tags to typeof(string)
  | where Tags startswith 'drop-by'
```

### <a name="drop-all-tags-matching-specific-regex"></a>특정 regex와 일치 하는 모든 태그 삭제 

테이블의 익스텐트에서 regex와 일치 하는 모든 태그를 삭제 합니다 `drop-by:StreamCreationTime_20160915(\d{6})` `MyTable` .

```kusto
.drop extent tags <| 
  .show table MyTable extents 
  | where isnotempty(Tags)
  | extend Tags = split(Tags, '\r\n')
  | mv-expand Tags to typeof(string)
  | where Tags matches regex @"drop-by:StreamCreationTime_20160915(\d{6})"
```

## <a name="sample-output"></a>샘플 출력

|OriginalExtentId |ResultExtentId | ResultExtentTags | 설명
|---|---|---|---
|e133f050-a1e2-4dad-8552-1f5cf47cab69 |0d96ab2d-9dd2-4d2c-a45e-b24c65aa6687 | Partition001 |
|cdbeb35b-87ea-499f-b545-defbae091b57 |a90a303c-8a14-4207-8f35-d8ea94ca45be | |
|4fcb4598-9a31-4614-903c-0c67c286da8c |97aafea1-59ff-4312-b06b-08f42187872f | Partition001 Partition002 |
|2dfdef64-62a3-4950-a130-96b5b1083b5a |0fb7f3da-5e28-4f09-a000-e62eb41592df | |
