---
title: 구체화 된 뷰 명령 표시-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 구체화 된 뷰 명령 표시에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: yifats
ms.service: data-explorer
ms.topic: reference
ms.date: 08/30/2020
ms.openlocfilehash: 4a9b42410c7a64a54ced0dc326b33242b4b11870
ms.sourcegitcommit: 21dee76964bf284ad7c2505a7b0b6896bca182cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/23/2020
ms.locfileid: "91057206"
---
# <a name="show-materialized-views-commands"></a>. 구체화 된 뷰 표시 명령

다음은 [구체화 된 뷰에](materialized-view-overview.md)대 한 정보를 표시 하는 명령입니다.

## <a name="show-materialized-view"></a>. 구체화 된 뷰 표시

구체화 된 뷰의 정의 및 현재 상태에 대 한 정보를 표시 합니다.

### <a name="syntax"></a>구문

`.show``materialized-view` *MaterializedViewName*

`.show` `materialized-views`

### <a name="properties"></a>속성

|속성|형식|설명
|----------------|-------|---|
|MaterializedViewName|String|구체화 된 뷰의 이름입니다.|

### <a name="example"></a>예제

```kusto
.show materialized-view ViewName
```

### <a name="output"></a>출력

|출력 매개 변수 |형식 |설명
|---|---|---
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
|EffectiveDateTime|Datetime|만든 시간 동안 확인 된 뷰의 개시 날짜 시간입니다 ( [구체화 된 뷰 만들기](materialized-view-create.md#create-materialized-view)참조).

## <a name="show-materialized-view-schema"></a>. 구체화 된 뷰 스키마 표시

CSL/JSON에서 구체화 된 뷰의 스키마를 반환 합니다.

### <a name="syntax"></a>구문

`.show``materialized-view` *MaterializedViewName*`cslschema`

`.show``materialized-view` *MaterializedViewName* `schema` MaterializedViewName `as``json`

`.show``materialized-view` *MaterializedViewName* `schema` MaterializedViewName `as``csl`

### <a name="output-parameters"></a>출력 매개 변수

| 출력 매개 변수 | 형식   | Description                                               |
|------------------|--------|-----------------------------------------------------------|
| TableName        | String | 구체화 된 뷰의 이름입니다.                        |
| 스키마           | 문자열 | 구체화 된 뷰 csl 스키마                          |
| DatabaseName     | String | 구체화 된 뷰가 속한 데이터베이스입니다.       |
| 폴더           | String | 구체화 된 보기의 폴더                                |
| DocString        | String | 구체화 된 뷰의 docstring                             |

## <a name="show-materialized-view-extents"></a>. 구체화 된 뷰 범위 표시

구체화 된 뷰의 *구체화* 된 부분에 있는 익스텐트를 반환 합니다. *구체화* 된 부분에 대 한 정의는 [구체화 된 보기의 작동 방식](materialized-view-overview.md#how-materialized-views-work)을 참조 하세요.

이 명령은 [테이블 범위 표시](../show-extents.md#table-level) 명령과 동일한 세부 정보를 제공 합니다.

### <a name="syntax"></a>구문

`.show``materialized-view` *MaterializedViewName* `extents` [ `hot` ]
 
## <a name="show-materialized-view-failures"></a>. 구체화 된 뷰 오류 표시

구체화 된 뷰의 구체화 프로세스의 일부로 발생 한 오류를 반환 합니다.

### <a name="syntax"></a>구문

`.show``materialized-view` *MaterializedViewName*`failures`

### <a name="properties"></a>속성

|속성|형식|설명
|----------------|-------|---|
|MaterializedViewName|String|구체화 된 뷰의 이름입니다.|

### <a name="output"></a>출력

|출력 매개 변수 |형식 |설명
|---|---|---
|Name  |타임스탬프 |오류 타임 스탬프입니다.
|OperationId  |String |실패 한 실행의 작업 ID입니다.
|Name|String|구체화 된 뷰 이름입니다.
|LastSuccessRun|Datetime|성공적으로 완료 된 마지막 실행 타임 스탬프입니다.
|FailureKind|String|실패 유형입니다.
|세부 정보|String|오류 세부 정보입니다.

