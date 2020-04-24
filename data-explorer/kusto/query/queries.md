---
title: 쿼리 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 쿼리에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 2ac338600320d3f83a92e22e405f630ee308df2f
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81510781"
---
# <a name="queries"></a>쿼리

쿼리는 Kusto Engine 클러스터의 수집된 데이터에 대한 읽기 전용 작업입니다. 쿼리는 항상 클러스터의 특정 데이터베이스컨텍스트에서 실행됩니다(다른 데이터베이스또는 다른 클러스터의 데이터도 참조할 수 있음).

Kusto의 최상위 시나리오인 Kusto 쿼리 언어 구문은 데이터에 대한 쿼리를 작성하고 실행하고 각 쿼리가 수행하는 작업을 논리적으로 명확하게 이해할 수 있도록 최적화되어 있습니다.

언어 구문은 "데이터"가 실제로 "테이블 형식 데이터"(하나 이상의 행/열 직사각형 모양의 데이터)를 의미하는 데이터 흐름의 구문입니다. 최소한 쿼리는 소스 데이터 참조(Kusto 테이블에 대한 참조)와 연산자 구분을 구분하기 위해 파이프 문자()를`|`사용하여 시각적으로 표시된 하나 이상의 **쿼리 연산자로** 구성됩니다.

다음은 그 예입니다.

```kusto
StormEvents 
| where State == 'FLORIDA' and StartTime > datetime(2000-01-01)
| count
```
    
파이프 문자 `|` 이 접두사로 붙는 각 필터는 일부 매개 변수를 가진 *연산자*의 인스턴스입니다. 연산자에 대한 입력은 앞에 붙는 파이프라인의 결과인 테이블입니다. 대부분의 경우 모든 매개 변수는 입력 열에 대한 [스칼라 식](./scalar-data-types/index.md) 입니다.
매개 변수가 입력 열의 이름인 경우도 있고 매개 변수가 두 번째 테이블인 경우도 있습니다. 테이블에 열 및 행이 한 개만 있더라도 쿼리의 결과는 언제나 테이블입니다.

## <a name="reference-query-operators"></a>참조: 쿼리 연산자

> `T` 은(는) 앞에 붙는 파이프라인 또는 원본 테이블을 나타내기 위해 사용됩니다.