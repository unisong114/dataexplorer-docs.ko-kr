---
title: 연산자 걸릴 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기에서 연산자 수행에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 0c8f724e139e13bf9ece00d5af09f2a3cf25b03a
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81506599"
---
# <a name="take-operator"></a>take 연산자

지정된 행 수로 되돌아갑니다.

```kusto
T | take 5
```

원본 데이터가 정렬되지 않는 한 반환되는 레코드를 보장할 수 없습니다.

**구문**

`take`*번호Ofrows* 
 `limit` *번호Ofrows*

(동의어입니다.)`take` `limit`

**참고 사항**

`take`데이터를 대화식으로 탐색할 때 작은 레코드 샘플을 볼 수 있는 간단하고 빠르며 효율적인 방법이지만 데이터 집합이 변경되지 않은 경우에도 여러 번 실행해도 결과의 일관성이 보장되지 는 않습니다.

심지어 쿼리에 의해 반환되는 행의 수는 쿼리에 의해 `take` 명시적으로 제한되지 않습니다 (연산자가 사용되지 않음), Kusto는 기본적으로 해당 숫자를 제한합니다.
자세한 내용은 [Kusto 쿼리 제한을](../concepts/querylimits.md) 참조하십시오.

참조: [정렬 연산자](sortoperator.md)
[상단 연산자](topoperator.md)
[상위 중첩 연산자](topnestedoperator.md)

## <a name="does-kusto-support-paging-of-query-results"></a>Kusto는 쿼리 결과의 페이징을 지원합니까?

Kusto는 내장 페이징 메커니즘을 제공하지 않습니다.

Kusto는 거대한 데이터 집합에 비해 우수한 쿼리 성능을 제공하기 위해 저장하는 데이터를 지속적으로 최적화하는 복잡한 서비스입니다. 페이징은 제한된 리소스를 가진 상태 비수기 클라이언트에 유용한 메커니즘이지만 클라이언트 상태 정보를 추적해야 하는 백 엔드 서비스로 부담을 이동합니다. 그 후 백 엔드 서비스의 성능과 확장성은 심각하게 제한됩니다.

페이징 지원의 경우 다음 기능 중 하나를 구현합니다.

* 쿼리 결과를 외부 저장소로 내보내고 생성된 데이터를 페이징합니다.

* Kusto 쿼리의 결과를 캐싱하여 상태 조정 API를 제공하는 중간 계층 응용 프로그램을 작성합니다.