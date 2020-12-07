---
title: 구체화 된 뷰 삭제-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 구체화 된 뷰 삭제 명령을 설명 합니다.
services: data-explorer
author: orspod
ms.author: yifats
ms.reviewer: yifats
ms.service: data-explorer
ms.topic: reference
ms.date: 08/30/2020
ms.openlocfilehash: 312b8dbd15f9ee570d1693f7bdbb77b9988d8207
ms.sourcegitcommit: 80f0c8b410fa4ba5ccecd96ae3803ce25db4a442
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/30/2020
ms.locfileid: "96320617"
---
# <a name="drop-materialized-view"></a>.drop materialized-view 

구체화 된 뷰를 삭제 합니다.

[데이터베이스 관리자](../access-control/role-based-authorization.md) 또는 구체화 된 뷰 관리자 권한이 필요 합니다.

## <a name="syntax"></a>Syntax

`.drop``materialized-view` *MaterializedViewName*

## <a name="properties"></a>속성

| 속성 | 형식| 설명 |
|----------------|-------|-----|
| MaterializedViewName| String| 구체화 된 뷰의 이름입니다.|

## <a name="returns"></a>반환

이 명령은 [구체화 된 뷰 표시](materialized-view-show-commands.md#show-materialized-view) 명령의 출력 인 데이터베이스에서 나머지 구체화 된 뷰를 반환 합니다.

## <a name="example"></a>예제

```kusto
.drop materialized-view ViewName
```

## <a name="output"></a>출력

|출력 매개 변수 |형식 |설명
|---|---|---|
|Name  |String |구체화 된 뷰의 이름입니다.
|SourceTable|String|구체화 된 뷰의 원본 테이블입니다.
|쿼리|String|구체화 된 뷰 쿼리입니다.
|MaterializedTo|Datetime|원본 테이블에서 구체화 된 최대 ingestion_time () 타임 스탬프입니다. 자세한 내용은 [구체화 된 뷰 작동 방식](materialized-view-overview.md#how-materialized-views-work)을 참조 하세요.
|My.settings.lastrun|Datetime |마지막으로 구체화를 실행 한 시간입니다.
|LastRunResult|String|마지막 실행의 결과입니다. `Completed`성공한 실행에 대해를 반환 하 고, 그렇지 않으면를 반환 `Failed` 합니다.
|I시트|bool|`True` 뷰가 정상으로 간주 되 면이 고, `False` 그렇지 않으면입니다. 최근 시간 (보다 큼)까지 구체화 된 뷰는 정상으로 간주 됩니다 `MaterializedTo` `ago(1h)` .
|IsEnabled|bool|`True` 뷰를 사용할 수 있는 경우 ( [구체화 된 뷰 사용 또는 사용 안 함](materialized-view-enable-disable.md))를 참조 하세요.
|폴더|문자열|구체화 된 뷰 폴더입니다.
|DocString|문자열|구체화 된 뷰 doc 문자열입니다.
|AutoUpdateSchema|bool|자동 업데이트에 대해 보기를 사용할지 여부를 지정 합니다.
|EffectiveDateTime|Datetime|만든 시간 동안 확인 된 뷰의 개시 날짜 시간입니다 (참조). [`.create materialized-view`](materialized-view-create.md#create-materialized-view)
