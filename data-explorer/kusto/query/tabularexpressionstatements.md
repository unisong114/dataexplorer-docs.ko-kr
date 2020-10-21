---
title: 테이블 형식 식 문-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 테이블 형식 식 문에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 943281c2e06aecfffab72ca0f98597c19938d936
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92250757"
---
# <a name="tabular-expression-statements"></a>테이블 형식 식 명령문

테이블 형식 식 문은 일반적으로 쿼리에 대 한 정보를 제공 하는 사용자에 게 유의 합니다. 이 문은 일반적으로 문 목록에 마지막으로 나타나며 입력 및 출력은 모두 테이블이 나 테이블 형식 데이터 집합으로 구성 됩니다.

Kusto는 테이블 형식 식 문에 데이터 흐름 모델을 사용 합니다. 테이블 형식 식 문의 일반적인 구조는 테이블 *형식 데이터 원본* (예: Kusto 테이블), *테이블 형식 데이터 연산자* (예: 필터 및 프로젝션), 잠재적으로 *렌더링 연산자*의 조합입니다. 컴퍼지션은 파이프 문자 ()로 표시 되어 `|` , 테이블 형식 데이터의 흐름을 왼쪽에서 오른쪽으로 시각적으로 나타내는 매우 일반적인 형태를 문에 제공 합니다.
각 연산자는 “파이프의” 테이블 형식 데이터 세트와 연산자 본문의 추가 입력(다른 테이블 형식 데이터 세트 포함)을 허용한 다음, 뒤에 오는 다음 연산자로 테이블 형식 데이터 세트를 내보냅니다.    

*source1* `|` *operator1* `|` *operator2* `|` *Renderinstruction*

다음 예에서는 원본 `Logs` (현재 데이터베이스의 테이블에 대 한 참조)이 고, 첫 번째 연산자는 `where` (레코드 별 조건자에 따라 입력에서 레코드를 필터링 하는 경우), 두 번째 연산자는 `count` (입력 데이터 집합의 레코드 수를 계산 하는)입니다.

```kusto
Logs | where Timestamp > ago(1d) | count
```

더 복잡 한 다음 예에서는 `join` 두 입력 데이터 집합의 레코드를 결합 하는 데 연산자를 사용 합니다. 하나는 테이블의 필터이 `Logs` 고 다른 하나는 테이블에 대 한 필터입니다 `Events` .

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

**테이블 형식 데이터 원본** 에서는 **테이블 형식 데이터 연산자**를 통해 추가로 처리할 레코드 집합을 생성 합니다. Kusto는 다음과 같은 다양 한 소스를 지원 합니다.

* 테이블 참조 (컨텍스트 데이터베이스 또는 다른 클러스터/데이터베이스에서 Kusto 테이블을 참조)
* 테이블 형식 [범위 연산자](rangeoperator.md)입니다.
* [Print 연산자](printoperator.md)입니다.
* 테이블을 반환 하는 함수 호출입니다.
* [테이블 리터럴](datatableoperator.md) ("datatable").