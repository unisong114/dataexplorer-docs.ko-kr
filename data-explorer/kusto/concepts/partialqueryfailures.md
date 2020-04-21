---
title: 부분 쿼리 실패 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 부분 쿼리 실패에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: d65256c0ac50a80e3e3b095e8d2348dbae5e585b
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81523123"
---
# <a name="partial-query-failures"></a>부분 쿼리 오류

*부분 쿼리 오류는* 쿼리가 실제 실행 단계를 시작한 후에만 검색되는 쿼리를 실행하지 못하는 실패입니다. 그 때까지 Kusto는 이미 HTTP 상태 `200 OK` 줄을 클라이언트에 반환하 고 "다시 가져 갈 수 없습니다", 그래서 그것은 클라이언트에 쿼리 결과 다시 전달 하는 결과 스트림에 실패를 표시 해야 합니다. (실제로 일부 결과 데이터를 호출자에게 이미 반환했을 수 있습니다.)

부분 쿼리 오류에는 여러 종류가 있습니다.
* [런어웨이 쿼리](runawayqueries.md): 너무 많은 리소스를 차지하는 쿼리입니다.
* [결과 잘림](resulttruncation.md): 결과 집합이 일부 제한을 초과함에 따라 잘린 쿼리입니다.
* [오버플로 :](overflow.md)오버플로 오류를 트리거하는 쿼리입니다.

부분 쿼리 실패는 다음 두 가지 방법 중 하나로 클라이언트에 다시 보고할 수 있습니다.

* 결과 데이터의 일부로(특별한 종류의 레코드는 이것이 데이터 자체가 아님을 나타냅니다). 이것이 기본 방법입니다.
* 결과 스트림의 "쿼리 상태" 테이블의 일부로. 이 작업은 요청 `deferpartialqueryfailures` 의 `properties` 슬롯()에서`Kusto.Data.Common.ClientRequestProperties.OptionDeferPartialQueryFailures`옵션을 사용하여 수행됩니다.
  이렇게 하는 클라이언트는 서비스에서 전체 결과 스트림을 사용하고, `QueryStatus` 결과를 찾고, 이 결과에 있는 레코드가 `Severity` `2` 없거나 더 작지 않은지 확인해야 합니다. 