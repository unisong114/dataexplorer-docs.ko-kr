---
title: 연속 데이터 내보내기 아티팩트 표시-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에 연속 데이터 내보내기 아티팩트를 표시 하는 방법을 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: yifats
ms.service: data-explorer
ms.topic: reference
ms.date: 08/03/2020
ms.openlocfilehash: 68b33ffc46df1e3bc4f63f8f7e9c86786116308b
ms.sourcegitcommit: c7b16409995087a7ad7a92817516455455ccd2c5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/12/2020
ms.locfileid: "88149474"
---
# <a name="show-continuous-export-artifacts"></a>연속 내보내기 아티팩트 표시

모든 실행에서 연속 내보내기로 내보낸 모든 아티팩트를 반환 합니다. 명령의 타임 스탬프 열을 기준으로 결과를 필터링 하 여 관심 있는 레코드만 볼 수 있습니다. 내보낸 아티팩트의 기록은 14 일 동안 보존 됩니다. 

## <a name="syntax"></a>구문

`.show``continuous-export` *ContinuousExportName*`exported-artifacts`

## <a name="properties"></a>속성

| 속성             | Type   | Description                |
|----------------------|--------|----------------------------|
| ContinuousExportName | String | 연속 내보내기의 이름입니다. |

## <a name="output"></a>출력

| 출력 매개 변수  | Type     | Description                            |
|-------------------|----------|----------------------------------------|
| 타임스탬프         | DateTime | 연속 내보내기 실행의 타임 스탬프 |
| ExternalTableName | String   | 외부 테이블의 이름             |
| 경로              | String   | 출력 경로                            |
| NumRecords        | long     | 경로로 내보낸 레코드 수     |

## <a name="example"></a>예제

```kusto
.show continuous-export MyExport exported-artifacts | where Timestamp > ago(1h)
```

| 타임스탬프                   | ExternalTableName | 경로             | NumRecords | SizeInBytes |
|-----------------------------|-------------------|------------------|------------|-------------|
| 2018-12-20 07:31:30.2634216 | ExternalBlob      | `http://storageaccount.blob.core.windows.net/container1/1_6ca073fd4c8740ec9a2f574eaa98f579.csv` | 10                          | 1024              |
