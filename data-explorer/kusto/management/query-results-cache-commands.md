---
title: 쿼리 결과 캐시 명령-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 쿼리 결과 캐시에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: amitof
ms.service: data-explorer
ms.topic: reference
ms.date: 06/16/2020
ms.openlocfilehash: 519560f0b6bb98651dc4a8b6749935ec9843eb9c
ms.sourcegitcommit: 5e903c61e779f7bf62f745f13a6038ce2a32e934
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88512578"
---
# <a name="query-results-cache-commands"></a>쿼리 결과 캐시 명령

쿼리 결과 캐시는 쿼리 결과를 저장 하는 전용 캐시입니다. 자세한 내용은 [쿼리 결과 캐시](../query/query-results-cache.md)를 참조 하세요.

**쿼리 결과 캐시 명령**

Kusto는 캐시 관리 및 관찰성 두 가지 명령을 제공 합니다.

* [`Show cache`](show-query-results-cache-command.md):이 명령을 사용 하 여 결과 캐시에서 노출 하는 통계를 표시 합니다.

* [`Clear cache(rhs:string)`](clear-query-results-cache-command.md):이 명령을 사용 하 여 캐시 된 결과를 지웁니다.
