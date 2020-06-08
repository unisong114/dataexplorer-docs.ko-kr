---
title: 쿼리 일관성-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 쿼리 일관성을 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 01/20/2019
ms.openlocfilehash: 8b4d1df4dc9a035764f9d50a4f9c4dcf452da67e
ms.sourcegitcommit: 188f89553b9d0230a8e7152fa1fce56c09ebb6d6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/08/2020
ms.locfileid: "84512268"
---
# <a name="query-consistency"></a>쿼리 일관성

Kusto는 **강력** 하 고 **약한**두 가지 쿼리 일관성 모델을 지원 합니다.

강력 하 게 일관 된 쿼리 (기본값)에는 "읽기-변경" 보장이 있습니다. 제어 명령을 보내고 명령이 성공적으로 완료 되었음을 확인 하는 경우 바로 다음에 나오는 쿼리가 명령의 결과를 확인 합니다.

클라이언트에서 명시적으로 사용 하도록 설정 해야 하는 약하게 일관 된 쿼리를 반드시 사용 해야 하는 것은 아닙니다. 쿼리를 생성 하는 클라이언트는 변경 내용과 이러한 변경을 반영 하는 쿼리 사이에 몇 가지 대기 시간 (일반적으로 1-2 분)을 관찰할 수

약하게 일관 된 쿼리의 장점은 데이터베이스 변경을 처리 하는 클러스터 노드의 부하를 줄이는 것입니다. 일반적으로는 먼저 강력한 일관성 모델을 사용해 보는 것이 좋습니다. 필요한 경우에만 약하게 일관 된 쿼리를 사용 하도록 전환 합니다.

약한 일관성 쿼리로 전환 하려면 `queryconsistency` [REST API 호출할](../api/rest/request.md)때 속성을 설정 합니다. .NET 클라이언트의 사용자는 [Kusto 연결 문자열](../api/connection-strings/kusto.md) 또는 [클라이언트 요청 속성](../api/netfx/request-properties.md)에서 플래그로 설정할 수도 있습니다.
