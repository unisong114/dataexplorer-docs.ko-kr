---
title: 창 함수-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 창 함수에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/20/2019
ms.openlocfilehash: d876f26de796008e83b620e4511a31cdb4e23888
ms.sourcegitcommit: 974d5f2bccabe504583e387904851275567832e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/18/2020
ms.locfileid: "83550693"
---
# <a name="window-functions"></a>창 함수

창 함수는 한 번에 행 집합에서 여러 행 (레코드)에 대해 작동 합니다. 집계 함수와 달리 창 함수는 행 집합의 행을 serialize 해야 합니다 (특정 순서로 정렬). 창 함수는 결과를 확인 하는 순서에 따라 달라질 수 있습니다.

창 함수는 직렬화 된 집합에만 사용할 수 있습니다. 행 집합을 serialize 하는 가장 쉬운 방법은 [serialize 연산자](./serializeoperator.md)를 사용 하는 것입니다. 이 연산자는 임의 방식으로 행의 순서를 "고정" 합니다. 직렬화 된 행의 순서가 의미상 중요할 경우 [sort 연산자](./sortoperator.md) 를 사용 하 여 특정 순서를 적용 합니다.

Serialization 프로세스에는 관련 된 특수 한 비용이 있습니다. 예를 들어 많은 시나리오에서 쿼리 병렬 처리를 방지할 수 있습니다. 따라서 불필요 하 게 직렬화를 적용 하지 마십시오. 필요한 경우 가능한 가장 작은 행 집합에 대 한 serialization을 수행 하도록 쿼리를 다시 정렬 합니다.

## <a name="serialized-row-set"></a>직렬화 된 행 집합

임의 행 집합 (예: 테이블 또는 테이블 형식 연산자의 출력)은 다음 중 한 가지 방법으로 serialize 할 수 있습니다.

1. 행 집합을 정렬 합니다. 정렬 된 행 집합을 내보내는 연산자의 목록은 아래를 참조 하십시오.
2. [Serialize 연산자](./serializeoperator.md)를 사용 합니다.

많은 테이블 형식 연산자는 입력이 이미 serialize 될 때마다 출력을 serialize 합니다. 연산자가 결과를 직렬화 하는 것을 보장 하지 않는 경우에도 마찬가지입니다. 예를 들어이 속성은 [확장 연산자](./extendoperator.md), [프로젝트 연산자](./projectoperator.md)및 [where 연산자](./whereoperator.md)에 대해 보장 됩니다.

## <a name="operators-that-emit-serialized-row-sets-by-sorting"></a>정렬 하 여 직렬화 된 행 집합을 내보내는 연산자

* [order 연산자](./orderoperator.md)
* [sort 연산자](./sortoperator.md)
* [top 연산자](./topoperator.md)
* [top-hitters 연산자](./tophittersoperator.md)
* [top-nested 연산자](./topnestedoperator.md)

## <a name="operators-that-preserve-the-serialized-row-set-property"></a>직렬화 된 행 집합 속성을 유지 하는 연산자

* [extend 연산자](./extendoperator.md)
* [mv-expand 연산자](./mvexpandoperator.md)
* [parse 연산자](./parseoperator.md)
* [project 연산자](./projectoperator.md)
* [project-away 연산자](./projectawayoperator.md)
* [project-rename 연산자](./projectrenameoperator.md)
* [take 연산자](./takeoperator.md)
* [where 연산자](./whereoperator.md)
