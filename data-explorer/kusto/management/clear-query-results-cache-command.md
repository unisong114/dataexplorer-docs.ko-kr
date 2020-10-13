---
title: 쿼리 결과 캐시 지우기-Azure 데이터 탐색기
description: Azure 데이터 탐색기에서 캐시 된 쿼리 결과를 지우는 방법에 대해 알아봅니다. 사용할 명령을 알아보고 예제를 확인 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: amitof
ms.service: data-explorer
ms.topic: reference
ms.date: 06/16/2020
ms.openlocfilehash: 72678453211ada2a6366b771153eeb11a717d7a3
ms.sourcegitcommit: 3d9b4c3c0a2d44834ce4de3c2ae8eb5aa929c40f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/13/2020
ms.locfileid: "92002944"
---
# <a name="clear-query-results-cache"></a>쿼리 결과 캐시 지우기

컨텍스트 데이터베이스에 대해 수행 [된 모든 캐시 된 쿼리 결과](../query/query-results-cache.md) 를 지웁니다.

**구문**

`.clear` `database` `cache` `query_results`

**반환**

이 명령은 다음 열이 있는 테이블을 반환 합니다.

|Column    |형식    |설명
|---|---|---
|NodeId|`string`|클러스터 노드의 식별자입니다.
|개수|`long`|노드에서 삭제 된 항목 수입니다.

**예제**

```kusto
.clear database cache queryresults
```

|NodeId|항목만|
|---|---|
|Node1|42
|Node2|0
