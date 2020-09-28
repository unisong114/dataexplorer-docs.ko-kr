---
title: 쿼리 결과 캐시 표시-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에 쿼리 결과 캐시를 표시 하는 방법을 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: amitof
ms.service: data-explorer
ms.topic: reference
ms.date: 06/16/2020
ms.openlocfilehash: e037b6ed01a2eba7c7370d75a4efea0f7cbc1756
ms.sourcegitcommit: 92b8057a36bd7daa16226f1526b29253bceb3602
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/28/2020
ms.locfileid: "91402721"
---
# <a name="show-database-cache-query_results"></a>. 데이터베이스 캐시 query_results 표시

컨텍스트 데이터베이스에 대해 만들어진 [쿼리 결과 캐시](../query/query-results-cache.md) 와 관련 된 통계를 보여 주는 테이블을 반환 합니다.

**구문**

`.show database cache query_results`

**출력**
 
|출력 매개 변수 |형식 |설명 
|---|---|---
|NodeId|`string`|클러스터 노드의 식별자입니다.
|적중 횟수  |`long`|캐시 적중 수입니다.
|누락  |`long`|캐시 누락 수입니다.
|CacheCapacityInBytes |`long` |캐시 용량 (바이트)입니다.
|UsedBytes  |`long` |캐시에서 사용 된 공간입니다.
|개수  |`long`| 캐시에 저장 된 고유한 쿼리 결과의 수입니다.
