---
title: IngestionTime 정책-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 IngestionTime 정책에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: 50e0083b1cdbed06106507fe69fb0d039c923c43
ms.sourcegitcommit: 9fe6e34ef3321390ee4e366819ebc9b132b3e03f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/01/2020
ms.locfileid: "84257810"
---
# <a name="ingestiontime-policy"></a>IngestionTime 정책

IngestionTime 정책은 테이블에 설정 (설정) 할 수 있는 선택적 정책입니다.

사용 하도록 설정 하면 Kusto가 `datetime` 테이블에 숨겨진 열을 추가 `$IngestionTime` 합니다. 이제 새 데이터를 수집 때마다 수집 시간이 숨겨진 열에 기록 됩니다. 이 시간은 Kusto 클러스터에서 데이터를 커밋하기 직전으로 측정 됩니다. 

> [!NOTE]
> 모든 레코드에는 고유한 `$IngestionTime` 값이 있습니다.

수집 시간 열이 숨겨져 있으므로 해당 값을 직접 쿼리할 수 없습니다.
대신 [ingestion_time ()](../query/ingestiontimefunction.md) 라는 특수 함수에서 해당 값을 검색 합니다. `datetime`테이블에 열이 없거나 레코드가 수집 때 IngestionTime 정책이 설정 되지 않은 경우 null 값이 반환 됩니다.

IngestionTime 정책은 두 가지 주요 시나리오를 위해 설계 되었습니다.
* 사용자가 수집 데이터의 대기 시간을 예상할 수 있도록 합니다.
  로그 데이터가 있는 많은 테이블에는 타임 스탬프 열이 있습니다. 타임 스탬프 값은 원본에 의해 채워지고 레코드가 생성 된 시간을 나타냅니다. 해당 열의 값을 수집 시간 열과 비교 하 여에서 데이터를 가져올 때 까지의 대기 시간을 예측할 수 있습니다. 
  
  > [!NOTE]
  > 원본 및 Kusto가 반드시 clock을 동기화 해야 하는 것은 아니므로 계산 된 값은 예상 값 뿐입니다.
  
* 사용자가 연속 쿼리를 실행할 수 있도록 하는 [데이터베이스 커서](../management/databasecursor.md) 를 지원 하기 위해 쿼리는 이전 쿼리 이후 수집 된 데이터로 제한 됩니다.

자세한 내용은 [IngestionTime 정책을 관리 하는 제어 명령을](../management/ingestiontime-policy.md)참조 하세요.
