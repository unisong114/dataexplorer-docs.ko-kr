---
title: . drop 익스텐트-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 범위 삭제 명령을 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 07/02/2020
ms.openlocfilehash: dfa462ca82cd5e94adff77b3893b3b02d60c6cdc
ms.sourcegitcommit: d6f35df833d5b4f2829a8924fffac1d0b49ce1c2
ms.contentlocale: ko-KR
ms.lasthandoff: 07/07/2020
ms.locfileid: "86060725"
---
# <a name="drop-extents"></a>. 범위 삭제

지정 된 데이터베이스 또는 테이블에서 익스텐트를 삭제 합니다.

이 명령에는 여러 변형이 있습니다. 즉, 삭제할 익스텐트가 Kusto 쿼리로 지정 됩니다. 다른 변형에서 익스텐트는 아래에 설명 된 미니 언어를 사용 하 여 지정 됩니다.

제공 된 쿼리에서 반환한 익스텐트가 있는 각 테이블에 대 한 [테이블 관리자 권한이](../management/access-control/role-based-authorization.md) 필요 합니다.

> [!NOTE]
> 데이터 분할은 Kusto에서 **익스텐트에서** 호출 되며 모든 명령은 "익스텐트" 또는 "익스텐트"를 동의어로 사용 합니다.
> 익스텐트에 대 한 자세한 내용은 [익스텐트 (데이터 분할) 개요](extents-overview.md)를 참조 하세요.

## <a name="drop-extents-with-a-query"></a>쿼리를 사용 하 여 익스텐트 삭제

Kusto 쿼리를 사용 하 여 지정 된 Drop 익스텐트가 있습니다.
"ExtentId" 라는 열이 포함 된 레코드 집합이 반환 됩니다.

을 사용 하는 경우 `whatif` 실제로 삭제 하지 않고만 보고 합니다.

### <a name="syntax"></a>Syntax

`.drop``extents`[ `whatif` ] <| *쿼리*

## <a name="drop-a-specific-extent"></a>특정 익스텐트 삭제

테이블 이름이 지정 된 경우 [테이블 관리자 권한이](../management/access-control/role-based-authorization.md) 필요 합니다.

테이블 이름을 지정 하지 않은 경우 [데이터베이스 관리자 권한이](../management/access-control/role-based-authorization.md) 필요 합니다.

### <a name="syntax"></a>Syntax

`.drop``extent` *ExtentId* [ `from` *TableName*]

## <a name="drop-specific-multiple-extents"></a>특정 다중 익스텐트 삭제

테이블 이름이 지정 된 경우 [테이블 관리자 권한이](../management/access-control/role-based-authorization.md) 필요 합니다.

테이블 이름을 지정 하지 않은 경우 [데이터베이스 관리자 권한이](../management/access-control/role-based-authorization.md) 필요 합니다.

### <a name="syntax"></a>Syntax

`.drop``extents` `(` *ExtentId1* `,` ... *Extentidn* `)` [ `from` *TableName*]

## <a name="drop-extents-by-specified-properties"></a>지정 된 속성에 따라 익스텐트 삭제

명령은 명령이 실행 되었지만 실제로 실행 되지 않는 것 처럼 출력을 생성 하는 에뮬레이션 모드를 지원 합니다. `.drop-pretend` 대신 `.drop`를 사용합니다.

테이블 이름이 지정 된 경우 [테이블 관리자 권한이](../management/access-control/role-based-authorization.md) 필요 합니다.

테이블 이름을 지정 하지 않은 경우 [데이터베이스 관리자 권한이](../management/access-control/role-based-authorization.md) 필요 합니다.

### <a name="syntax"></a>Syntax

`.drop``extents`[ `older` *N* ( `days`  |  `hours` )] `from` (*TableName*  |  `all` `tables` ) [ `trim` `by` ( `extentsize`  |  `datasize` ) *N* ( `MB`  |  `GB`  |  `bytes` )] [ `limit` *개수 개수*]

* `older`: *N* 일/시간 보다 오래 된 익스텐트만 삭제 됩니다.
* `trim`: 익스텐트의 합계가 필요한 크기 (MaxSize)와 일치할 때까지 작업에서 데이터베이스의 데이터를 자릅니다.
* `limit`: 작업은 첫 번째 한도 *개수* 범위에 적용 됩니다.

## <a name="examples"></a>예

### <a name="remove-all-extents-by-time-created"></a>만든 시간에 따라 모든 익스텐트 제거

데이터베이스의 모든 테이블에서 10 일 이전에 만든 모든 익스텐트를 제거 합니다.`MyDatabase`

```kusto
.drop extents <| .show database MyDatabase extents | where CreatedOn < now() - time(10d)
```

### <a name="remove-some-extents-by-time-created"></a>만든 시간별 범위 제거

테이블의 모든 익스텐트 `Table1` 를 제거 하 고 `Table2` 생성 시간이 10 일 이전인 경우

```kusto
.drop extents older 10 days from tables (Table1, Table2)
```

### <a name="emulation-mode-show-which-extents-would-be-removed-by-the-command"></a>에뮬레이션 모드: 명령에 의해 제거 되는 익스텐트를 표시 합니다.

>[!NOTE]
>익스텐트 ID 매개 변수는이 명령에 적용할 수 없습니다.

```kusto
.drop-pretend extents older 10 days from all tables
```

### <a name="remove-all-extents-from-testtable"></a>' TestTable '에서 모든 익스텐트 제거

```kusto
.drop extents from TestTable
```

## <a name="return-output"></a>반환 출력

|출력 매개 변수 |형식 |설명 
|---|---|---
|ExtentId |String |명령으로 인해 삭제 된 ExtentId
|TableName |String |범위가 속한 테이블 이름  
|Adventureworks.createdon |DateTime |범위가 처음 생성 된 시간에 대 한 정보를 포함 하는 타임 스탬프
 
## <a name="sample-output"></a>샘플 출력

|익스텐트 ID |테이블 이름 |만든 날짜 
|---|---|---
|43c6e03f-1713-4ca7-a52a-5db8a4e8b87d |TestTable |2015-01-12 12:48:49.4298178
