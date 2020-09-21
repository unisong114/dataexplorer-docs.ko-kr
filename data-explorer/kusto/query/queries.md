---
title: 쿼리-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 쿼리를 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: d1527208b81c1bd3bbf5fa93a251f7acf4b44662
ms.sourcegitcommit: 97404e9ed4a28cd497d2acbde07d00149836d026
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/21/2020
ms.locfileid: "90832538"
---
# <a name="query-operators"></a>쿼리 연산자

쿼리는 Kusto Engine 클러스터의 수집 데이터에 대 한 읽기 전용 작업입니다. 쿼리는 항상 클러스터에 있는 특정 데이터베이스의 컨텍스트에서 실행 됩니다. 다른 데이터베이스의 데이터 또는 다른 클러스터 에서도 참조할 수 있습니다.

데이터의 임시 쿼리는 Kusto에 대 한 우선 순위가 가장 높은 시나리오로, Kusto 쿼리 언어 구문은 데이터에 대해 쿼리를 작성 하 고 실행 하는 비 전문 사용자에 게 최적화 되어 있으며 각 쿼리가 수행 하는 작업 (논리적)을 명확 하 게 파악할 수 있습니다.

언어 구문은 데이터 흐름의 의미입니다. 여기서 "data"는 "테이블 형식 데이터" (하나 이상의 행/열 직사각형 모양의 데이터)를 의미 합니다. 최소한 쿼리는 원본 데이터 참조 (Kusto 테이블에 대 한 참조)와 시퀀스에서 적용 되는 하나 이상의 **쿼리 연산자로** 구성 됩니다 .이는 파이프 문자 ()를 사용 하 여 연산자를 구분 하 여 시각적으로 표시 `|` 합니다.

예를 들면 다음과 같습니다.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents 
| where State == 'FLORIDA' and StartTime > datetime(2000-01-01)
| count
```

파이프 문자 `|` 이 접두사로 붙는 각 필터는 일부 매개 변수를 가진 *연산자*의 인스턴스입니다. 연산자에 대한 입력은 앞에 붙는 파이프라인의 결과인 테이블입니다. 대부분의 경우 모든 매개 변수는 입력 열에 대한 [스칼라 식](./scalar-data-types/index.md) 입니다.
매개 변수가 입력 열의 이름인 경우도 있고 매개 변수가 두 번째 테이블인 경우도 있습니다. 테이블에 열 및 행이 한 개만 있더라도 쿼리의 결과는 언제나 테이블입니다.

`T` 는 이전 파이프라인 또는 원본 테이블을 나타내기 위해 쿼리에서 사용 됩니다.
