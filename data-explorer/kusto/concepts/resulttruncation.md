---
title: Kusto 쿼리 결과 집합이 내부 한계를 초과 합니다. Azure 데이터 탐색기
description: 이 문서에서는 쿼리 결과 집합이 내부 ... Azure 데이터 탐색기 제한.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 448da9db3e515385ca44cec94d9dd8604a8c1125
ms.sourcegitcommit: ee904f45e3eb3feab046263aa9956cb7780a056d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92356574"
---
# <a name="query-result-set-has-exceeded-the-internal--limit"></a>쿼리 결과 집합이 내부 ... 제한

*쿼리 결과 집합이 내부 ... 제한은* 쿼리 결과가 다음 두 제한 중 하나를 초과 했을 때 발생 하는 [부분 쿼리 실패](partialqueryfailures.md) 의 일종입니다.
* 레코드 수에 대 한 제한 ( `record count limit` 기본적으로 50만로 설정)
* 총 데이터 양에 대 한 한도 ( `data size limit` 기본적으로 67108864 (64mb)로 설정)

몇 가지 가능한 작업 과정은 다음과 같습니다.

* 리소스를 적게 사용 하도록 쿼리를 변경 합니다. 
  예를 들어, 다음을 수행할 수 있습니다.
  * [Take 연산자](../query/takeoperator.md) 또는 [where 절](../query/whereoperator.md) 추가를 사용 하 여 쿼리에서 반환 되는 레코드 수를 제한 합니다.
  * 쿼리에서 반환 되는 열 수를 줄여 보십시오. 프로젝트 [연산자](../query/projectoperator.md), [프로젝트 자리 비움 연산자](../query/projectawayoperator.md)또는 [프로젝트-유지 연산자](../query/project-keep-operator.md) 를 사용 합니다.
  * [요약 연산자](../query/summarizeoperator.md) 를 사용 하 여 집계 된 데이터 가져오기
* 해당 쿼리에 대해 일시적으로 관련 쿼리 제한을 늘립니다. 자세한 내용은 [쿼리 제한](querylimits.md)에서 **결과 잘림** 참조를 참조 하세요.

 > [!NOTE] 
 > 클러스터를 보호 하기 위해 제한이 존재 하므로 쿼리 제한을 늘리지 않는 것이 좋습니다. 한도는 단일 쿼리가 클러스터에서 실행 중인 동시 쿼리를 방해 하지 않는지 확인 합니다.
  
