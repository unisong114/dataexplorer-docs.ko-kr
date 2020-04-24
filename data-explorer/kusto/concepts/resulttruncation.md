---
title: 쿼리 결과 집합이 내부를 초과했습니다... 제한 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 쿼리 결과 집합이 내부를 초과했습니다... Azure 데이터 탐색기에서 제한됩니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: efb07e68922a88e2cf59ef1eefeabd3af085aed4
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81523021"
---
# <a name="query-result-set-has-exceeded-the-internal--limit"></a>쿼리 결과 집합이 내부를 초과했습니다... 제한

*쿼리 결과 집합이 내부를 초과했습니다... 제한은* 쿼리 결과가 두 제한 중 하나를 초과할 때 발생하는 [일종의 부분 쿼리 실패입니다.](partialqueryfailures.md)
* 레코드 수 제한(기본적으로`record count limit`500,000으로 설정).
* 총 데이터 양(기본적으로`data size limit`67,108,864(64MB)으로 설정)에 대한 제한입니다. 

이 경우 다음과 같은 몇 가지 가능한 작업 과정이 있습니다.
* 쿼리를 변경하여 리소스를 더 적게 소비합니다. 예를 들어 쿼리에서 반환되는 레코드 수를 제한하거나(테이크 [연산자](../query/takeoperator.md) 사용 또는 [추가 where where 절](../query/whereoperator.md)추가) 쿼리에서 반환되는 열 수를 줄이거나(프로젝트 [연산자](../query/projectoperator.md) 또는 [프로젝트 어웨이 연산자](../query/projectawayoperator.md)사용) [요약 연산자](../query/summarizeoperator.md) 등을 사용하여 집계된 데이터를 얻을 수 있습니다.
* 해당 쿼리에 대한 관련 쿼리 제한을 일시적으로 늘립니다(쿼리 [제한](querylimits.md) **아래의 결과 잘림** 참조).  
  단일 쿼리가 클러스터에서 실행되는 동시 쿼리를 방해하지 않도록 클러스터를 보호하기 위한 제한이 있기 때문에 일반적으로 권장되지 않습니다.