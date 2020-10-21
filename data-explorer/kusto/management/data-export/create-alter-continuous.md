---
title: 연속 데이터 내보내기 만들기 또는 변경-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 연속 데이터 내보내기를 만들거나 변경 하는 방법을 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: yifats
ms.service: data-explorer
ms.topic: reference
ms.date: 08/03/2020
ms.openlocfilehash: 11822a58b2ced0b257ad649997bc154ab4be19d5
ms.sourcegitcommit: 898f67b83ae8cf55e93ce172a6fd3473b7c1c094
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/21/2020
ms.locfileid: "92337587"
---
# <a name="create-or-alter-continuous-export"></a>연속 내보내기 만들기 또는 변경

연속 내보내기 작업을 만들거나 변경 합니다.

## <a name="syntax"></a>구문

`.create-or-alter``continuous-export` *ContinuousExportName* <br>
[ `over` `(` *T1*, *T2* `)` ] <br>
`to``table` *Externaltablename* <br> [ `with` `(` *PropertyName* `=` *PropertyValue* `,` ... `)` ]<br>
\<| *쿼리입니다*

## <a name="properties"></a>속성

| 속성             | 유형     | 설명   |
|----------------------|----------|---------------------------------------|
| ContinuousExportName | String   | 연속 내보내기의 이름입니다. 이름은 데이터베이스 내에서 고유 해야 하며 주기적으로 연속 내보내기를 실행 하는 데 사용 됩니다.      |
| ExternalTableName    | String   | 내보낼 [외부 테이블](../external-table-commands.md) 의 이름입니다.  |
| 쿼리                | String   | 내보낼 쿼리입니다.  |
| over (T1, T2)        | String   | 쿼리에서 팩트 테이블의 쉼표로 구분 된 목록 (옵션)입니다. 지정 하지 않으면 쿼리에서 참조 되는 모든 테이블이 팩트 테이블로 간주 됩니다. 지정 된 경우이 목록에 *없는* 테이블은 차원 테이블로 처리 되며 범위가 지정 되지 않습니다. 모든 레코드는 모든 내보내기에 참여 합니다. 자세한 내용은 [연속 데이터 내보내기 개요](continuous-data-export.md) 를 참조 하세요. |
| 간격 끝점  | Timespan | 연속 내보내기 실행 사이의 시간 범위입니다. 1 분 보다 커야 합니다.   |
| forcedLatency        | Timespan | 이 기간 이전에만 수집 된 레코드로 쿼리를 제한 하는 선택적 기간 (현재 시간 기준)입니다. 이 속성은 예를 들어 쿼리가 일부 집계/조인을 수행 하 고 내보내기를 실행 하기 전에 모든 관련 레코드가 이미 수집 되었는지 확인 하는 경우에 유용 합니다.

위의 외에도 [외부 테이블로 내보내기 명령](export-data-to-an-external-table.md) 에서 지원 되는 모든 속성은 연속 내보내기 만들기 명령에서 지원 됩니다. 

## <a name="example"></a>예제

```kusto
.create-or-alter continuous-export MyExport
over (T)
to table ExternalBlob
with
(intervalBetweenRuns=1h, 
 forcedLatency=10m, 
 sizeLimit=104857600)
<| T
```

| Name     | ExternalTableName | 쿼리 | ForcedLatency | 간격 끝점 | CursorScopedTables         | ExportProperties                   |
|----------|-------------------|-------|---------------|---------------------|----------------------------|------------------------------------|
| MyExport | ExternalBlob      | S     | 00:10:00      | 01:00:00            | [<br>  "[' DB ']. ['S '] "<br>] | {<br>  "SizeLimit": 104857600<br>} |