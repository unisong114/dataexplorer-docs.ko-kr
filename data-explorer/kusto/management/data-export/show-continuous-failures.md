---
title: 연속 데이터 내보내기 오류 표시-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 연속 데이터 내보내기 오류를 표시 하는 방법을 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: yifats
ms.service: data-explorer
ms.topic: reference
ms.date: 08/03/2020
ms.openlocfilehash: 54c95e3beecd25b504c52c2fe06ffa51160ac8ca
ms.sourcegitcommit: c7b16409995087a7ad7a92817516455455ccd2c5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/12/2020
ms.locfileid: "88149467"
---
# <a name="show-continuous-export-failures"></a>연속 내보내기 오류 표시

연속 내보내기의 일부로 기록 된 모든 오류를 반환 합니다. 명령의 타임 스탬프 열을 기준으로 결과를 필터링 하 여 관심 있는 시간 범위만 볼 수 있습니다. 

## <a name="syntax"></a>구문

`.show``continuous-export` *ContinuousExportName*`failures`

## <a name="properties"></a>속성

| 속성             | Type   | Description                |
|----------------------|--------|----------------------------|
| ContinuousExportName | String | 연속 내보내기 이름  |

## <a name="output"></a>출력

| 출력 매개 변수 | Type      | Description                                         |
|------------------|-----------|-----------------------------------------------------|
| 타임스탬프        | DateTime  | 오류의 타임 스탬프입니다.                           |
| OperationId      | String    | 오류의 작업 ID입니다.                    |
| 속성             | String    | 연속 내보내기 이름입니다.                             |
| LastSuccessRun   | 타임스탬프 | 연속 내보내기의 마지막 실행 성공입니다.   |
| FailureKind      | String    | 실패/PartialFailure입니다. PartialFailure는 오류가 발생 하기 전에 일부 아티팩트가 성공적으로 내보내진 것을 나타냅니다. |
| 세부 정보          | String    | 오류 세부 정보입니다.                              |

## <a name="example"></a>예제 

```kusto
.show continuous-export MyExport failures 
```

| 타임스탬프                   | OperationId                          | 속성     | LastSuccessRun              | FailureKind | 세부 정보    |
|-----------------------------|--------------------------------------|----------|-----------------------------|-------------|------------|
| 2019-01-01 11:07:41.1887304 | ec641435-2505-4532-ba19-d6ab88c96a9d | MyExport | 2019-01-01 11:06:35.6308140 | 실패     | 세부 정보 ... |
