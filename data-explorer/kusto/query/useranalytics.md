---
title: 사용자 분석 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 사용자 분석에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 11/17/2019
ms.openlocfilehash: 37b5962b9441c3eb7362a239404189e489b1c3ff
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81504984"
---
# <a name="user-analytics"></a>사용자 분석

이 섹션에서는 사용자 분석 시나리오에 대한 Kusto 익스텐션(플러그인)에 대해 설명합니다.

|시나리오|플러그 인|세부 정보|설치 환경|
|--------|------|--------|-------|
| 시간에 따라 새 사용자 수 계산 | [activity_counts_metrics](activity-counts-metrics-plugin.md)|각 시간 창에 대한 개수/dcounts/새 카운트를 반환합니다. 각 시간 창은 *모든* 이전 시간 창과 비교됩니다.|쿠스토.익스플로러: 보고서 갤러리|
| 기간별: 보존/이탈률 및 신규 사용자 | [activity_metrics](activity-metrics-plugin.md)|각 시간 창에 대한 카운트, 보존/커른 비율을 반환합니다. 각 시간 창이 *이전* 시간 창과 비교됩니다.|쿠스토.익스플로러: 보고서 갤러리|
| 슬라이딩 윈도우를 통해 사용자 수 및 카운트 | [sliding_window_counts](sliding-window-counts-plugin.md)|각 시간 창에 대해 슬라이딩 창 방식으로 조회 기간 동안 개수 및 카운트를 반환합니다.|
| 신규 사용자 코호트: 유지/이탈률 및 신규 사용자 | [new_activity_metrics](new-activity-metrics-plugin.md)|새로운 사용자 집단(시간 창에서 1위를 본 모든 사용자)을 비교합니다. 각 코호트는 모든 이전 코호트와 비교됩니다. 비교는 *모든* 이전 시간 창을 고려합니다.|쿠스토.익스플로러: 보고서 갤러리|
|활성 사용자: 고유 개수 |[active_users_count](active-users-count-plugin.md)|사용자가 spcified 조회 기간 중 최소 X 개의 고유 기간에 나타나는 경우에만 고려되는 각 시간 창에 대해 고유 사용자를 반환합니다.|
|사용자 참여: DAU/WAU/MAU|[activity_engagement](activity-engagement-plugin.md)|컴퓨팅 참여(예: DAU/WAU)를 위한 내부 시간 창(예: 일일)과 외부(예: 주간) 간의 비교|쿠스토.익스플로러: 보고서 갤러리|
|세션수: 활성 세션 수|[session_count](session-count-plugin.md)|세션이 기간별로 정의된 세션 수 - 사용자 레코드가 현재 레코드의 회수 기간에 보이지 않은 경우 새 세션으로 간주됩니다.|
||||
|유입경로: 이전 및 다음 상태 시퀀스 분석 | [funnel_sequence](funnel-sequence-plugin.md)|일련의 이벤트를 수행한 고유 사용자와 시퀀스를 이끈 이전/다음 이벤트를 계산합니다. [sankey 다이어그램을](https://en.wikipedia.org/wiki/Sankey_diagram) 생성하는 데 유용합니다.||
|유입경로: 시퀀스 완료 분석|[funnel_sequence_completion](funnel-sequence-completion-plugin.md)|각 시간 창에서 지정된 시퀀스를 *완료한* 사용자 고유 수를 계산합니다.|
||||