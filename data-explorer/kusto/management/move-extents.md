---
title: . 익스텐트 이동-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 익스텐트 이동 명령을 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 07/02/2020
ms.openlocfilehash: 994f7077b1583317320cc561b2d5a5ae1cbe829f
ms.sourcegitcommit: d6f35df833d5b4f2829a8924fffac1d0b49ce1c2
ms.contentlocale: ko-KR
ms.lasthandoff: 07/07/2020
ms.locfileid: "86060712"
---
# <a name="move-extents"></a>. 익스텐트 이동

이 명령은 특정 데이터베이스의 컨텍스트에서 실행 됩니다. 원본 테이블에서 대상 테이블로 지정 된 익스텐트를 이동 합니다.

이 명령을 사용 하려면 원본 및 대상 테이블에 대 한 [테이블 관리자 권한이](../management/access-control/role-based-authorization.md) 있어야 합니다.

> [!NOTE]
> 데이터 분할은 Kusto에서 **익스텐트에서** 호출 되며 모든 명령은 "익스텐트" 또는 "익스텐트"를 동의어로 사용 합니다.
> 익스텐트에 대 한 자세한 내용은 [익스텐트 (데이터 분할) 개요](extents-overview.md)를 참조 하세요.

## <a name="syntax"></a>Syntax

`.move`[ `async` ] `extents` `all` `from` `table` *Sourcetablename* `to` `table` *destinationtablename*

`.move`[ `async` ] `extents` `(` *GUID1* [ `,` *GUID2* `)` ] `from` `table` *Sourcetablename* `to` `table` *Destinationtablename* 

`.move`[ `async` ] `extents` `to` `table` *Destinationtablename*  <|  *쿼리*

`async`(선택 사항). 명령을 비동기적으로 실행 합니다. 
   * 작업 ID (Guid)가 반환 됩니다.
   * 작업 상태를 모니터링할 수 있습니다. [. Show operations](operations.md#show-operations) 명령을 사용 합니다.
   * 성공적으로 실행 한 결과를 검색할 수 있습니다. [. 작업 세부 정보 표시](operations.md#show-operation-details) 명령을 사용 합니다.

이동할 익스텐트를 지정 하는 방법에는 다음 세 가지가 있습니다.
* 특정 테이블의 모든 익스텐트를 이동 합니다.
* 원본 테이블에서 범위 Id를 명시적으로 지정 합니다.
* 원본 테이블에서 익스텐트 Id를 지정 하는 결과가 포함 된 쿼리를 제공 합니다.

## <a name="restrictions"></a>제한 사항

* 원본 테이블과 대상 테이블은 모두 컨텍스트 데이터베이스에 있어야 합니다.
* 원본 테이블의 모든 열은 동일한 이름 및 데이터 형식을 사용 하는 대상 테이블에 있어야 합니다.

## <a name="specify-extents-with-a-query"></a>쿼리를 사용 하 여 익스텐트 지정

```kusto
.move extents to table TableName <| ...query...
```

익스텐트는 *ExtentId*라는 열이 포함 된 레코드 집합을 반환 하는 Kusto 쿼리를 사용 하 여 지정 됩니다.

## <a name="return-output-for-sync-execution"></a>반환 출력 (동기화 실행의 경우)

출력 매개 변수 |형식 |설명
---|---|---
OriginalExtentId |string |원본 테이블에서 대상 테이블로 이동 된 원본 범위의 고유 식별자 (GUID)입니다.
ResultExtentId |string |원본 테이블에서 대상 테이블로 이동한 결과 범위에 대 한 고유 식별자 (GUID)입니다. 오류가 발생 한 경우-"Failed".
설명 |string |작업이 실패 하는 경우 오류 세부 정보를 포함 합니다.

## <a name="examples"></a>예

### <a name="move-all-extents"></a>모든 익스텐트 이동 

테이블의 모든 익스텐트 `MyTable` 를 테이블로 이동 `MyOtherTable` :

```kusto
.move extents all from table MyTable to table MyOtherTable
```

### <a name="move-two-specific-extents"></a>두 개의 특정 익스텐트 이동 

익스텐트 Id로 두 개의 특정 익스텐트를 테이블에서 테이블로 이동 `MyTable` 합니다 `MyOtherTable` .

```kusto
.move extents (AE6CD250-BE62-4978-90F2-5CB7A10D16D7,399F9254-4751-49E3-8192-C1CA78020706) from table MyTable to table MyOtherTable
```

### <a name="move-all-extents-from-specific-tables"></a>특정 테이블에서 모든 익스텐트 이동 

특정 tavles (,)의 모든 `MyTable1` 익스텐트 `MyTable2` 를 테이블로 이동 합니다 `MyOtherTable` .

```kusto
.move extents to table MyOtherTable <| .show tables (MyTable1,MyTable2) extents
```

## <a name="sample-output"></a>샘플 출력

|OriginalExtentId |ResultExtentId| 설명
|---|---|---
|e133f050-a1e2-4dad-8552-1f5cf47cab69 |0d96ab2d-9dd2-4d2c-a45e-b24c65aa6687| 
|cdbeb35b-87ea-499f-b545-defbae091b57 |a90a303c-8a14-4207-8f35-d8ea94ca45be| 
|4fcb4598-9a31-4614-903c-0c67c286da8c |97aafea1-59ff-4312-b06b-08f42187872f| 
|2dfdef64-62a3-4950-a130-96b5b1083b5a |0fb7f3da-5e28-4f09-a000-e62eb41592df| 
