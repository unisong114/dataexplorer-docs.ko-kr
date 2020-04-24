---
title: 표식 식 문 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 테이블 형식 표현식 문을 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 1209f96ed99de79d3fa6ac00e3a115a00a6248e6
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81506616"
---
# <a name="tabular-expression-statements"></a>테이블 형식 식 명령문

표식 표현문은 쿼리에 대해 이야기할 때 일반적으로 염두에 두어야 할 내용입니다. 이 문은 일반적으로 문 목록에서 마지막으로 나타나며 입력과 출력은 테이블 또는 테이블 형식 데이터 집합으로 구성됩니다.

Kusto는 테이블 형식 표현문에 대한 데이터 흐름 모델을 사용합니다. 테이블 식 문의 일반적인 구조는 테이블 *형식 데이터* 원본(예: Kusto 테이블), *테이블 형식 데이터 연산자(예:* 필터 및 프로젝션) 및 잠재적으로 *렌더링 연산자의 컴포지션입니다.* 컴포지션은 파이프 문자()로`|`표시되어 문에게 왼쪽에서 오른쪽으로 표 데이터의 흐름을 시각적으로 나타내는 매우 규칙적인 형태를 제공합니다.
각 연산자는 "파이프에서" 테이블 형식 데이터 세트를 수락하고 연산자의 본문에서 추가 입력(다른 테이블 형식 데이터 세트 포함)을 허용한 다음 다음과 같은 다음 연산자로 테이블 형식 데이터 집합을 내보립니다.   

*source1* `|` *operator1* `|` *연산자2* `|` *렌더지시*

다음 예제에서 소스는 `Logs` (현재 데이터베이스의 테이블에 대한 참조), 첫 `where` 번째 연산자는 (일부 레코드별 술어에 따라 입력에서 레코드를 `count` 필터링하는) 및 두 번째 연산자는 (입력 데이터 집합의 레코드 수를 계산합니다)입니다.

```kusto
Logs | where Timestamp > ago(1d) | count
```

다음보다 복잡한 예제에서는 `join` 연산자가 테이블의 필터인 두 입력 데이터 집합과 `Logs` `Events` 테이블의 필터인 레코드를 결합하는 데 사용됩니다.

```kusto
Logs 
| where Timestamp > ago(1d) 
| join 
(
    Events 
    | where continent == 'Europe'
) on RequestId 
```

## <a name="tabular-data-sources"></a>테이블 형식 데이터 원본

**테이블 형식 데이터 원본은** 테이블 형식 데이터 연산자가 추가로 처리할 레코드 **집합을 생성합니다.** Kusto는 다음과 같은 여러 소스를 지원합니다.

* 테이블 참조(컨텍스트 데이터베이스 또는 다른 클러스터/데이터베이스에서 Kusto 테이블을 참조합니다.)
* 테이블 [범위 연산자](rangeoperator.md).
* [인쇄 연산자](printoperator.md).
* 테이블을 반환하는 함수의 호출입니다.
* [테이블 리터럴("데이터](datatableoperator.md) 테이블")입니다.