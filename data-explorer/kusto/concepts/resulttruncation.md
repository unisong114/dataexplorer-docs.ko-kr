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
ms.openlocfilehash: 41a9851405ff85210bba7728a3737fc3b0a57f70
ms.sourcegitcommit: fd3bf300811243fc6ae47a309e24027d50f67d7e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/13/2020
ms.locfileid: "83382389"
---
# <a name="query-result-set-has-exceeded-the-internal--limit"></a>쿼리 결과 집합이 내부 ... 제한

*쿼리 결과 집합이 내부 ... 제한은* 쿼리 결과가 다음 두 제한 중 하나를 초과 했을 때 발생 하는 [부분 쿼리 실패](partialqueryfailures.md) 의 일종입니다.
* 레코드 수에 대 한 제한 `record count limit` 입니다. 기본값은 50만로 설정 됩니다.
* 데이터의 총 크기에 대 한 제한 `data size limit` 입니다. 기본적으로 67108864 (64mb)로 설정 됩니다. 

이 경우 다음과 같은 몇 가지 작업 과정을 수행할 수 있습니다.
* 리소스를 적게 사용 하도록 쿼리를 변경 합니다. 예를 들어, 쿼리에서 반환 하는 레코드 수를 제한 ( [take 연산자](../query/takeoperator.md) 사용 또는 [where 절](../query/whereoperator.md)추가) 하거나 쿼리에서 반환 되는 열 수를 줄이거나 ( [프로젝트 운영자](../query/projectoperator.md) 또는 [프로젝트 자리](../query/projectawayoperator.md)조치 사용) [요약 연산자](../query/summarizeoperator.md) 를 사용 하 여 집계 된 데이터를 가져올 수 있습니다.
* 해당 쿼리에 대해 일시적으로 관련 쿼리 제한을 늘립니다 ( [쿼리 제한](querylimits.md)에서 **결과 잘림** 참조).  
  이는 단일 쿼리가 클러스터에서 실행 중인 동시 쿼리를 방해 하지 않도록 하기 위해 클러스터를 특별히 보호 하기 위한 제한 사항이 있기 때문에 일반적으로 권장 되지 않습니다.