---
title: 행 순서 정책 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 행 순서 정책에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: 4dca1a4083a6141eb94d9bf3cf69f7134ebfca04
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81520216"
---
# <a name="row-order-policy"></a>행 순서 정책

행 순서 정책은 테이블에 설정된 선택적 정책으로, 데이터 샤드에서 원하는 행 순서에 대해 Kusto에 "힌트"를 제공합니다.

이 정책의 주요 목적은 압축을 개선하는 것이 아니라(잠재적인 부작용임에도 불구하고) 정렬된 열의 작은 값 하위 집합으로 좁혀지는 것으로 알려진 쿼리의 성능을 향상시키는 것입니다.

다음과 같은 경우에 정책을 적용하는 것이 적절합니다.
* 대부분의 쿼리는 특정 대형 차원 열의 특정 값(예: "응용 프로그램 ID" 또는 "테넌트 ID")을 필터링합니다.
* 테이블에 수집된 데이터는 이 열에 따라 미리 정렬될 수 없습니다.

정책의 일부로 정의할 수 있는 열(정렬 키)의 양에 대해 하드코딩된 제한이 설정되지는 않지만, 모든 추가 열은 섭취 프로세스에 약간의 오버헤드를 추가하고 더 많은 열이 추가됨에 따라 효과적인 반환이 줄어듭니다.

> [!NOTE]
> 정책이 테이블에 적용되면 해당 순간부터 수집된 데이터에 영향을 미칩니다.

행 주문 정책을 관리하기 위한 제어 명령은 [여기에서](../management/roworder-policy.md) 찾을 수 있습니다.