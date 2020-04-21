---
title: 쿼리 일관성 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 쿼리 일관성에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 01/20/2019
ms.openlocfilehash: b66540af2d2d4bef4571249474cd618e69eb2261
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81523106"
---
# <a name="query-consistency"></a>쿼리 일관성

Kusto는 두 가지 쿼리 일관성 모델인 **강성과** **약함**모델을 지원합니다.

강력하게 일관된 쿼리(기본값)에는 "읽기-내 변경 내용" 보장이 있습니다. 컨트롤 명령을 보내고 명령이 성공적으로 완료되었음을 긍정적으로 인정하는 클라이언트는 바로 다음의 모든 쿼리가 명령의 결과를 관찰할 수 있도록 보장합니다.

약하게 일관된 쿼리(클라이언트에서 명시적으로 활성화해야 함)는 보장을 하지 않습니다. 쿼리를 만드는 클라이언트는 변경 내용과 이러한 변경 내용을 반영하는 쿼리 간에 일부 대기 시간(일반적으로 1-2분)을 관찰할 수 있습니다.

약하게 일관된 쿼리의 장점은 데이터베이스 변경 내용을 처리하는 클러스터 노드의 부하를 줄인다는 것입니다. 일반적으로 고객은 먼저 강력하게 일관된 모델을 시도하고 절대적으로 필요한 경우 약한 일관성을 사용하는 것으로 전환하는 것이 좋습니다.

[REST API 호출을](../api/rest/request.md)수행할 때 `queryconsistency` 속성을 설정하여 약하게 일관된 쿼리로 전환합니다. Kusto .NET 클라이언트의 사용자는 [Kusto 연결 문자열또는](../api/connection-strings/kusto.md) [클라이언트 요청 속성의](../api/netfx/request-properties.md)플래그로 설정할 수도 있습니다.