---
title: 창 함수 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 창 함수에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/20/2019
ms.openlocfilehash: ab4f6da2478ba4de81b2034c1cb07458daa80bd0
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81504253"
---
# <a name="window-functions"></a>창 함수

창 함수는 한 번에 설정된 행의 여러 행(레코드)에서 작동합니다.
집계 함수와 달리 창 함수는 결과를 결정하는 순서에 따라 달라질 수 있기 때문에 행 집합의 행을 **직렬화해야** 합니다(특정 순서가 있음).

창 함수는 직렬화되지 않은 행 집합에서 사용할 수 없으며 쿼리에서 이러한 컨텍스트에서 사용할 때 오류를 제공합니다. 행 집합을 직렬화하는 가장 쉬운 방법은 [직렬화 연산자(일부](./serializeoperator.md)지정되지 않은 임의 방식으로) 행 순서를 단순히 "고정"하는 것입니다.
직렬화된 행의 순서가 본질적으로 중요한 경우 [정렬 연산자에서](./sortoperator.md) 특정 순서를 강제로 강제할 수 있습니다.

직렬화 프로세스에는 연관된 사소한 비용이 있습니다. 예를 들어 많은 시나리오에서 쿼리 병렬 처리가 방지될 수 있습니다. 따라서 직렬화를 불필요하게 적용하지 않으며 필요한 경우 가능한 가장 작은 행 집합에서 직렬화를 수행할 수 있도록 쿼리를 다시 정렬하는 것이 좋습니다.

## <a name="serialized-row-set"></a>직렬화된 행 집합

임의의 행 집합(예: 테이블 또는 테이블 연산자의 출력)은 다음 방법 중 하나로 직렬화될 수 있습니다.

1. 행 집합을 정렬합니다. 정렬된 행 집합을 내보하는 연산자 목록은 아래를 참조하십시오.
2. [직렬화 연산자](./serializeoperator.md).를 사용 하 여 .

많은 테이블 형식 연산자는 그 자체로 결과가 직렬화되는 것을 보장하지 는 않지만 입력이 직렬화되면 출력도 마찬가지라는 속성이 있습니다. 예를 들어 이 속성은 [확장 연산자,](./extendoperator.md) [프로젝트 연산자](./projectoperator.md)및 [where 연산자](./whereoperator.md)에 대해 보장됩니다.

## <a name="operators-that-emit-serialized-row-sets-by-sorting"></a>정렬하여 직렬화된 행 집합을 내보하는 연산자

* [order 연산자](./orderoperator.md)
* [sort 연산자](./sortoperator.md)
* [상단 연산자](./topoperator.md)
* [top-hitters 연산자](./tophittersoperator.md)
* [top-nested 연산자](./topnestedoperator.md)

## <a name="operators-that-preserve-the-serialized-row-set-property"></a>직렬화된 행 집합 속성을 보존하는 연산자

* [extend 연산자](./extendoperator.md)
* [mv 확장 연산자](./mvexpandoperator.md)
* [parse 연산자](./parseoperator.md)
* [project 연산자](./projectoperator.md)
* [project-away 연산자](./projectawayoperator.md)
* [project-rename 연산자](./projectrenameoperator.md)
* [take 연산자](./takeoperator.md)
* [where 연산자](./whereoperator.md)