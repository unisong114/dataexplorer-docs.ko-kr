---
title: 런어웨이 쿼리-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 런어웨이 쿼리를 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 4f2cf559863eecbd267855cd4a22f2dbe8673dc7
ms.sourcegitcommit: ee904f45e3eb3feab046263aa9956cb7780a056d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92356608"
---
# <a name="runaway-queries"></a>런어웨이 쿼리

*런어웨이 쿼리* 는 쿼리 실행 중 일부 내부 [쿼리 제한이](querylimits.md) 초과 될 때 발생 하는 일종의 [부분 쿼리 오류](partialqueryfailures.md) 입니다. 

예를 들어 다음과 같은 오류가 보고 될 수 있습니다. `HashJoin operator has exceeded the memory budget during evaluation. Results may be incorrect or incomplete.`

몇 가지 가능한 작업 강좌가 있습니다.
* 리소스를 적게 사용 하도록 쿼리를 변경 합니다. 예를 들어 쿼리 결과 집합이 너무 커서 오류가 표시 되는 경우 다음을 수행할 수 있습니다.
  * 쿼리에서 반환 하는 레코드 수 제한
     * [Take 연산자](../query/takeoperator.md) 사용
     * [Where 절](../query/whereoperator.md) 추가
  * 쿼리에서 반환 하는 열 수 줄이기
     * [Project 연산자](../query/projectoperator.md) 사용
     * [프로젝트 자리 연산자](../query/projectawayoperator.md) 사용
     * [프로젝트-keep 연산자](../query/project-keep-operator.md) 사용
  * [요약 연산자](../query/summarizeoperator.md) 를 사용 하 여 집계 된 데이터를 가져옵니다.
* 해당 쿼리에 대해 일시적으로 관련 쿼리 제한을 늘립니다. 자세한 내용은 [쿼리 제한-반복기 당 메모리 제한](querylimits.md)을 참조 하세요. 그러나이 방법은 권장 되지 않습니다. 클러스터를 보호 하 고 단일 쿼리가 클러스터에서 실행 중인 동시 쿼리를 방해 하지 않도록 하는 제한 사항이 있습니다.
