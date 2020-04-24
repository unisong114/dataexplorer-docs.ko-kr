---
title: 면 연산자 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 면 연산자에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 0b6c87fc044e0f00f77e28e85d89757a69835164
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81515320"
---
# <a name="facet-operator"></a>facet 연산자

지정된 각 열에 대해 하나씩 테이블 집합을 반환합니다.
각 표는 해당 열에서 가져온 값 목록을 지정합니다.
`with` 절을 사용하여 추가 테이블을 만들 수 있습니다.

**구문**

*T* `| facet by` 열`, ` *이름* [...] [`with (` *필터 파이프*`)`

**인수**

* *열 이름:* 출력 테이블로 요약할 입력의 열 이름입니다.
* *필터 파이프:* 출력 중 하나를 생성하기 위해 입력 테이블에 적용된 쿼리 식입니다.

**반환**

여러 테이블: 절에 `with` 대 한 하나, 각 열에 대 한 하나.

**예제**

```kusto
MyTable 
| facet by city, eventType 
    with (where timestamp > ago(7d) | take 1000)
```