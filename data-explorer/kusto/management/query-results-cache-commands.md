---
title: 쿼리 결과 캐시-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 쿼리 결과 캐시에 대해 설명 합니다.
services: data-explorer
author: amitof
ms.author: amitof
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: reference
ms.date: 06/16/2020
ms.openlocfilehash: 630266fdaaaac51037a99a6a5243db58a232ae48
ms.sourcegitcommit: a8575e80c65eab2a2118842e59f62aee0ff0e416
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/17/2020
ms.locfileid: "84943117"
---
# <a name="query-results-cache"></a>쿼리 결과 캐시

쿼리 결과 캐시는 쿼리 결과를 저장 하는 전용 캐시입니다. 자세한 내용은 [쿼리 결과 캐시](../query/query-results-cache.md)를 참조 하세요.

**쿼리 결과 캐시 명령**

Kusto는 캐시 관리 및 관찰성 두 가지 명령을 제공 합니다.

* [`Show cache`](show-query-results-cache-command.md):이 명령을 사용 하 여 결과 캐시에서 노출 하는 통계를 표시 합니다.

* [`Clear cache(rhs:string)`](clear-query-results-cache-command.md):이 명령을 사용 하 여 캐시 된 결과를 지웁니다.
