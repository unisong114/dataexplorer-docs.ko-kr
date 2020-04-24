---
title: 쿠스토 핑 REST API - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기에서 Kusto Ping REST API에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 14a3d3dd641ff435784eac4e813d98bac8c4a552
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81502587"
---
# <a name="kusto-ping-rest-api"></a>쿠스토 핑 레스트 API

ping REST API는 로드 밸런서와 같은 네트워크 장치가 클러스터의 상태 비이스프리스 프런트 엔드 구성 요소의 간단한 네트워크 응답성을 확인할 수 있도록 제공됩니다. GET 동사가 이 끝점에 적용되면 클러스터는 200 OK HTTP 응답을 반환하여 응답합니다. REST API는 인증되지 않습니다(호출자는 HTTP `Authorization` 헤더를 보낼 필요가 없습니다).

- 경로: `/v1/rest/ping`
- 동사:`GET`
- 작업: 메시지로 `200 OK` 응답
- 인수: 없음