---
title: 연속 데이터 내보내기 사용 또는 사용 안 함-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 지속적인 데이터 내보내기를 사용 하지 않도록 설정 하거나 사용 하도록 설정 하는 방법을 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: yifats
ms.service: data-explorer
ms.topic: reference
ms.date: 08/03/2020
ms.openlocfilehash: b69db144474557ab8d5a8e19a7bce9bbbd5df183
ms.sourcegitcommit: c7b16409995087a7ad7a92817516455455ccd2c5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/12/2020
ms.locfileid: "88149516"
---
# <a name="disable-or-enable-continuous-export"></a>연속 내보내기 사용 또는 사용 안 함

연속 내보내기 작업을 사용 하지 않거나 사용 하도록 설정 합니다. 비활성화 된 연속 내보내기는 실행 되지 않지만 현재 상태는 유지 되 고 연속 내보내기를 사용 하도록 설정 된 경우 다시 시작할 수 있습니다. 

장시간 사용 하지 않도록 설정 된 연속 내보내기를 사용 하도록 설정 하는 경우 내보내기를 사용 하지 않도록 설정할 때 마지막으로 중지 된 위치에서 내보내기가 계속 됩니다. 모든 프로세스를 처리 하기에 충분 한 클러스터 용량이 없는 경우이 연속 작업을 수행 하면 오래 실행 되는 내보내기가 발생할 수 있습니다. 연속 내보내기는 마지막 실행 시간을 기준으로 오름차순으로 실행 됩니다. 즉, 마지막 내보내기가 완료 될 때까지 가장 오래 된 내보내기가 먼저 실행 됩니다. 

## <a name="syntax"></a>구문

`.enable``continuous-export` *ContinuousExportName* 

`.disable``continuous-export` *ContinuousExportName* 

## <a name="properties"></a>속성

| 속성             | Type   | Description                |
|----------------------|--------|----------------------------|
| ContinuousExportName | String | 연속 내보내기 이름 |

## <a name="output"></a>출력

변경 된 연속 내보내기의 [연속 내보내기 표시 명령](show-continuous-export.md) 결과입니다. 
