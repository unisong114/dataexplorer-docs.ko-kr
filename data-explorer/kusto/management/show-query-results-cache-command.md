---
title: 쿼리 결과 캐시 표시-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에 쿼리 결과 캐시를 표시 하는 방법을 설명 합니다.
services: data-explorer
author: amitof
ms.author: amitof
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: reference
ms.date: 06/16/2020
ms.openlocfilehash: bcbdb59355ce0461d735cbea902551c219479fd2
ms.sourcegitcommit: a8575e80c65eab2a2118842e59f62aee0ff0e416
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/17/2020
ms.locfileid: "84943106"
---
# <a name="show-query-results-cache"></a>. 쿼리 결과 캐시 표시

[쿼리 결과 캐시](../query/query-results-cache.md)와 관련 된 통계를 보여 주는 테이블을 반환 합니다.

**구문**

`.show` `query` `results` `cache`

**출력**
 
|출력 매개 변수 |Type |설명 
|---|---|---
|적중 횟수  |long |캐시 적중 수입니다.
|누락  |long |캐시 누락 수입니다.
|CacheCapacityInBytes |long |캐시 용량 (바이트)입니다.
|UsedBytes  |long |캐시에서 사용 된 공간입니다.
|개수  |long | 캐시에 저장 된 고유한 쿼리 결과의 수입니다.

**제한 사항**

* 명령의 출력은 현재 요청이 배치는 노드에 의해 수집 된 캐시 통계만을 반영 합니다.
* 명령은 "최근" 기록도 표시 합니다.
