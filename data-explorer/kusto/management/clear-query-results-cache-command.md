---
title: 쿼리 결과 캐시 지우기-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 캐시 된 데이터베이스 스키마를 지우기 위한 관리 명령을 설명 합니다.
services: data-explorer
author: amitof
ms.author: amitof
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: reference
ms.date: 06/16/2020
ms.openlocfilehash: 68d6493176696f0241467303f166b8c7160859b7
ms.sourcegitcommit: cf1da667be12656a8c4727c23144421b5a4b1099
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/21/2020
ms.locfileid: "86565448"
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
