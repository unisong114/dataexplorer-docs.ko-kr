---
title: 사용자 분석-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 사용자 분석을 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 11/17/2019
ms.openlocfilehash: 5a9ca6259296f2fa2c5ad83622e7f3012169864e
ms.sourcegitcommit: b08b1546122b64fb8e465073c93c78c7943824d9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/06/2020
ms.locfileid: "85966978"
---
# <a name="user-analytics-plugins"></a>사용자 분석 플러그 인

이 섹션에서는 사용자 분석 시나리오에 대 한 Kusto 확장 (플러그 인)을 설명 합니다.

|시나리오|플러그 인|설명|사용자 환경|
|--------|------|--------|-------|
| 시간에 따른 새 사용자 수 계산 | [activity_counts_metrics](activity-counts-metrics-plugin.md)|각 기간에 대 한 개수/시간/새 개수를 반환 합니다. 각 기간 *을 이전 시간* 창과 비교 합니다.|Kusto 탐색기: 보고서 갤러리|
| 기간 초과: 보존/변동 률 및 새 사용자 | [activity_metrics](activity-metrics-plugin.md)|`dcount`각 기간에 대 한 보존/변동 률을 반환 합니다. 각 기간을 *이전* 시간 창과 비교할 때|Kusto 탐색기: 보고서 갤러리|
| 사용자 수 및 `dcount` 슬라이딩 윈도우 초과 | [sliding_window_counts](sliding-window-counts-plugin.md)|각 기간에 대해는 `dcount` 슬라이딩 윈도우 방식으로 count and lookback period를 반환 합니다.|
| 새 사용자 코 호트: 보존/변동 율 및 새 사용자 | [new_activity_metrics](new-activity-metrics-plugin.md)|새 사용자의 코 호트 (처음에는 시간 창에 표시 된 모든 사용자)를 비교 합니다. 각 코 호트 이전 코 호트 비교 됩니다. 비교 시 이전 시간 *모두* 고려|Kusto 탐색기: 보고서 갤러리|
|활성 사용자: 고유 카운트 |[active_users_count](active-users-count-plugin.md)|각 기간에 대해 고유한 사용자를 반환 합니다. 사용자는 지정 된 lookback 기간에 최소 X 개의 고유 기간에 표시 되는 경우에만 고려 됩니다.|
|사용자 참여: 6AU/WAU/MAU|[activity_engagement](activity-engagement-plugin.md)|내부 시간 창 (예: 매일)과 컴퓨팅 참여를 위한 외부 (예: 매주)를 비교 합니다 (예: WAU).|Kusto 탐색기: 보고서 갤러리|
|세션: 활성 세션 수|[session_count](session-count-plugin.md)|세션이 특정 기간에 의해 정의 되는 세션 수를 계산 합니다. 현재 레코드의 lookback 기간에 표시 되지 않은 사용자 레코드는 새 세션으로 간주 됩니다.|
||||
|Funnels: 이전 및 다음 상태 시퀀스 분석 | [funnel_sequence](funnel-sequence-plugin.md)|이벤트 시퀀스를 수행한 고유 사용자 수와 시퀀스를 발생 시킨 이전 또는 다음 이벤트를 계산 합니다. [Sankey 다이어그램](https://en.wikipedia.org/wiki/Sankey_diagram) 을 생성 하는 데 유용 합니다.||
|Funnels: 시퀀스 완성 분석|[funnel_sequence_completion](funnel-sequence-completion-plugin.md)|각 기간에 지정 된 시퀀스를 *완료* 한 고유한 사용자 수를 계산 합니다.|
||||