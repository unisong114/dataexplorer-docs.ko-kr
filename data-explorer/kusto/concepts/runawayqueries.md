---
title: 런어웨이 쿼리 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 런어웨이 쿼리에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 076afea64cf3c4405f37e9e4014584b90d58ca07
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81522987"
---
# <a name="runaway-queries"></a>런어웨이 쿼리

*런어웨이 쿼리는* 쿼리 실행 중에 일부 내부 [쿼리 제한을](querylimits.md) 초과할 때 발생하는 일종의 부분 쿼리 [실패입니다.](partialqueryfailures.md)

예를 들어 다음과 같은 오류가 보고될 수 있습니다.`HashJoin operator has exceeded the memory budget during evaluation. Results may be incorrect or incomplete.`

이 경우 다음과 같은 몇 가지 가능한 작업 과정이 있습니다.
* 쿼리를 변경하여 리소스를 더 적게 소비합니다. 예를 들어 쿼리 결과 집합이 너무 커서 오류가 표시되면 쿼리에서 반환하는 레코드 수를 제한 ( [take 연산자](../query/takeoperator.md) 사용 또는 [where 절](../query/whereoperator.md)추가) 하거나에서 퀴리를 이용하여 반환되는 열 수를 줄일 수 있습니다. ([프로젝트 연산자](../query/projectoperator.md) 또는 [프로젝트 어웨이 연산자](../query/projectawayoperator.md)를 사용), 아니면 [요약 연산자](../query/summarizeoperator.md)를 사용하여 집계된 데이터를 가져올 수 있습니다.
* 해당 쿼리에 대한 관련 쿼리 제한을 일시적으로 늘립니다(쿼리 [제한에](querylimits.md)따라 결과 집합 거동리 기만자당 **최대 메모리** 참조).  
  단일 쿼리가 클러스터에서 실행되는 동시 쿼리를 방해하지 않도록 클러스터를 보호하기 위한 제한이 있기 때문에 일반적으로 권장되지 않습니다.
  
  
  