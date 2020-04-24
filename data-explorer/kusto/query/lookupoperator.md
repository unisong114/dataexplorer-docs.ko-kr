---
title: 조회 연산자 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 조회 연산에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/12/2020
ms.openlocfilehash: 1325a1b839b62baacade4cf7c64cd278aeeabaf5
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81513059"
---
# <a name="lookup-operator"></a>lookup 연산자

연산자는 `lookup` 치수 테이블에서 조회된 값을 사용하여 팩트 테이블의 열을 확장합니다.

```kusto
FactTable | lookup kind=leftouter (DimensionTable) on CommonColumn, $left.Col1 == $right.Col2
```

여기서, 결과는 후자의 테이블에서 각 `FactTable` `$left`쌍 (, `DimensionTable` )과 `$right`함께 이전 테이블에서 각 쌍`CommonColumn`(,`Col`)의 조회를 수행함으로써`Col2`(참조)의 데이터로 (참조)를 확장하는 테이블이다.`CommonColumn1` 팩트 테이블과 차원 테이블간의 차이점은 [팩트 및 차원 테이블을](../concepts/fact-and-dimension-tables.md)참조하십시오. 

연산자는 `lookup` [조인 연산자와](joinoperator.md) 유사한 작업을 수행하며 다음과 같은 차이점을 가지고 있습니다.

* 결과는 조인 작업의 기초가 `$right` 되는 테이블의 열을 반복하지 않습니다.
* 두 종류의 조회만 `leftouter` 지원되며 `inner` `leftouter` 기본값인 경우 .
* 성능 면에서 시스템은 기본적으로 테이블이 `$left` 더 큰(팩트) 테이블이고 `$right` 테이블이 더 작은(차원) 테이블이라고 가정합니다. 이는 연산자가 사용하는 가정과 `join` 정반대입니다.
* 운영자는 `lookup` `$right` 테이블을 `$left` 테이블에 자동으로 브로드캐스트합니다(기본적으로 지정된 것처럼 `hint.broadcast` 행동함). 이렇게 하면 테이블 크기가 `$right` 제한됩니다.

**구문**

*왼쪽* `|` `lookup` 테이블`kind` `=` `leftouter`|`inner` `(` [()] *오른쪽 테이블* `)` `on` *속성*

**인수**

* *왼쪽 테이블:* 조회의 기초가 되는 테이블 또는 테이블 형식 식입니다.
  `$left`로 표시됩니다.

* *RightTable:* 팩트 테이블에서 새 열을 "채우는" 데 사용되는 테이블 또는 테이블 형식 식입니다. `$right`로 표시됩니다.

* *특성*: *LeftTable의* 행이 *RightTable의*행과 일치하는 방법을 설명하는 하나 이상의 규칙의 쉼표 구분 목록입니다. 논리 연산자를 `and` 사용하여 여러 규칙을 평가합니다.
  규칙은 다음 중 하나일 수 있습니다.

  |규칙 종류        |구문                                          |Predicate                                                      |
  |-----------------|------------------------------------------------|---------------------------------------------------------------|
  |이름으로 의한 같음 |*ColumnName*                                    |`where`*왼쪽 테이블*. *열 이름* `==` *오른쪽 테이블*. *열 이름*|
  |값별 같음|`$left.`*왼쪽열* `==` `$right.` *오른쪽 열*|`where``$left.` *왼쪽 열* `==` `$right.`*오른쪽 열        |

  > [!Note] 
  > '값별 같음'의 경우 열 이름은 표기명으로 표시된 해당 `$left` 소유자 `$right` 테이블과 함께 *정규화되어야 합니다.*

* `kind`: *오른쪽*테이블에서 일치하지 않는 *LeftTable의* 행을 처리하는 방법에 대한 선택적 지침입니다. 기본적으로 모든 `leftouter` 행이 연산자가 추가한 *RightTable* 열의 누락된 값에 사용되는 null 값으로 출력에 표시된다는 것을 의미합니다. 사용되는 `inner` 경우 이러한 행은 출력에서 생략됩니다. (다른 종류의 조인은 p `looku`연산자에서 지원되지 않습니다.)
  
**반환**

다음을 포함하고 있는 테이블:

* 일치하는 키를 포함하여 두 테이블 각각의 모든 열에 대한 열.
  이름 충돌이 있는 경우 오른쪽열의 이름이 자동으로 바뀝니다.
* 입력된 테이블 간의 모든 일치 항목에 대한 행. 일치 항목은 한 테이블에서 선택된 행이며 모든 `on` 필드에 대해 다른 테이블의 행과 같은 값을 가지고 있습니다. 
* 특성(조회 키)은 출력 테이블에 한 번만 나타납니다.

 * `kind`불특정`kind=leftouter`

     내부 일치 외에도 일치 항목이 없더라도 왼쪽(및/또는 오른쪽)의 모든 행에 대한 행이 있습니다. 이 경우 일치하지 않는 출력 셀에는 null 값이 포함되어 있습니다.

 * `kind=inner`

     왼쪽 및 오른쪽에서 일치하는 행의 모든 조합에 대해 한 개의 출력 행이 있습니다.

**예**

```kusto
let FactTable=datatable(Row:string,Personal:string,Family:string) [
  "1", "Bill",   "Gates",
  "2", "Bill",   "Clinton",
  "3", "Bill",   "Clinton",
  "4", "Steve",  "Ballmer",
  "5", "Tim",    "Cook"
];
let DimTable=datatable(Personal:string,Family:string,Alias:string) [
  "Bill",  "Gates",   "billg",
  "Bill",  "Clinton", "billc",
  "Steve", "Ballmer", "steveb",
  "Tim",   "Cook",    "timc"
];
FactTable
| lookup kind=leftouter DimTable on Personal, Family
```

행     | Personal  | 패밀리   | Alias
--------|-----------|----------|--------
1       | Bill      | 게이츠    | 지폐
2       | Bill      | 클린턴  | 빌크 (것)와
3       | Bill      | 클린턴  | 빌크 (것)와
4       | Steve     | 발머  | 스티브 (미국)
5       | 팀       | 요리     | 팀크 (팀)는