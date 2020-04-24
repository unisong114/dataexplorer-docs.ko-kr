---
title: 인비시타임 정책 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 인시션Time 정책에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: 0c1755115a9e9f4c60c7f5574eb9b784520ecb88
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81520828"
---
# <a name="ingestiontime-policy"></a>IngestionTime 정책

인기션Time 정책은 테이블에서 설정(사용)할 수 있는 선택적 정책입니다.

테이블에서 이 정책을 사용하도록 설정하면 Kusto는 `datetime` 테이블에 숨겨진 열을 `$IngestionTime`추가합니다. 이 시점부터 새 데이터가 테이블에 수집될 때마다 수집되는 모든 레코드에 대해 Kusto 클러스터에서 측정한 수집 시간이 숨겨진 열에 기록됩니다. 모든 레코드는 다른 열과 마찬가지로 고유한 `$IngestionTime` 값을 가합니다.

인기 시간 열이 숨겨져 있기 때문에 해당 값을 직접 쿼리할 수 없습니다.
대신 해당 값을 검색하기 위해 [ingestion_time()라는](../query/ingestiontimefunction.md) 특수 함수가 제공됩니다. 테이블에 이러한 열이 없거나 레코드를 인젝션할 때 IngestionTime 정책을 사용할 수 없는 경우 null 값이 반환됩니다.

인기션Time 정책은 다음 두 가지 주요 시나리오를 위해 설계되었습니다.
* 사용자가 데이터 수집에서 종단 간 대기 시간을 예측할 수 있도록 합니다.
  로그 데이터를 보유하는 많은 테이블에는 레코드가 생성된 시간을 나타내기 위해 소스에 의해 값이 채워지는 타임스탬프 열이 있습니다. 해당 열의 값을 수집 시간 열과 비교하면 데이터를 가져오는 데 대한 대기 시간을 예측할 수 있습니다. (소스와 Kusto가 반드시 시계가 동기화되지 는 않기 때문에 이것은 추정치일 뿐입니다.)
* 데이터베이스 [커서를](../management/databasecursor.md)지원하려면 사용자가 연속 쿼리를 발행하고 매번 쿼리를 이전 쿼리 이후 수집된 데이터로 제한할 수 있도록 합니다.



IngestionTime 정책을 관리하기 위한 제어 명령에 대한 자세한 내용은 [여기를 참조하십시오.](../management/ingestiontime-policy.md)