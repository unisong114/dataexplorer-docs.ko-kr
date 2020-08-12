---
title: 연속 데이터 내보내기 표시-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 연속 데이터 내보내기 속성을 표시 하는 방법을 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: yifats
ms.service: data-explorer
ms.topic: reference
ms.date: 08/03/2020
ms.openlocfilehash: db7583c055468808ade1166c0bfee90859399d32
ms.sourcegitcommit: c7b16409995087a7ad7a92817516455455ccd2c5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/12/2020
ms.locfileid: "88149359"
---
# <a name="show-continuous-export"></a>연속 내보내기 표시

*ContinuousExportName*의 연속 내보내기 속성을 반환 합니다. 

## <a name="syntax"></a>구문

`.show``continuous-export` *ContinuousExportName*

## <a name="properties"></a>속성

| 속성             | Type   | Description                |
|----------------------|--------|----------------------------|
| ContinuousExportName | String | 연속 내보내기의 이름입니다. |

`.show` `continuous-exports`

데이터베이스의 모든 연속 내보내기를 반환 합니다. 

## <a name="output"></a>출력

| 출력 매개 변수    | Type     | Description                                                             |
|---------------------|----------|-------------------------------------------------------------------------|
| CursorScopedTables  | String   | 명시적으로 범위가 지정 된 (팩트) 테이블 목록 (JSON 직렬화)               |
| ExportProperties    | String   | 속성 내보내기 (JSON 직렬화)                                     |
| ExportedTo          | DateTime | 성공적으로 내보낸 마지막 날짜/시간 (수집 시간)입니다.       |
| ExternalTableName   | String   | 외부 테이블의 이름                                              |
| ForcedLatency       | TimeSpan | 강제 대기 시간 (제공 되지 않은 경우 null)                                   |
| 간격 끝점 | TimeSpan | 실행 간 간격                                                   |
| IsDisabled          | 부울  | 연속 내보내기를 사용 하지 않도록 설정한 경우 True입니다.                               |
| IsRunning           | 부울  | 연속 내보내기가 현재 실행 중인 경우 True입니다.                      |
| LastRunResult       | String   | 마지막 연속 내보내기 실행 ( `Completed` 또는)의 결과 `Failed` |
| LastRunTime         | DateTime | 연속 내보내기가 마지막으로 실행 된 시간 (시작 시간)           |
| 속성                | String   | 연속 내보내기의 이름                                           |
| 쿼리               | String   | 내보내기 쿼리                                                            |
| StartCursor         | String   | 이 연속 내보내기에 대 한 첫 번째 실행의 시작 지점         |

