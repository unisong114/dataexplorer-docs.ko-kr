---
title: lookup 연산자-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 조회 연산자에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 03/12/2020
ms.openlocfilehash: 3305d78dd903160491ac3cdabd274ce8ace8ba2f
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92246419"
---
# <a name="lookup-operator"></a>lookup 연산자

`lookup`연산자는 팩트 테이블의 열을 차원 테이블에서 조회 한 값으로 확장 합니다.

```kusto
FactTable | lookup kind=leftouter (DimensionTable) on CommonColumn, $left.Col1 == $right.Col2
```

여기서 결과는 `FactTable` `$left` `DimensionTable` `$right` `CommonColumn` `Col` `CommonColumn1` `Col2` 두 번째 테이블의 각 쌍 (,)을 사용 하 여 이전 테이블에서 각 쌍 (,)을 조회 하 여 ()를 (참조)의 데이터로 확장 하는 테이블입니다. 팩트 테이블과 차원 테이블 간의 차이점은 [팩트 및 차원 테이블](../concepts/fact-and-dimension-tables.md)을 참조 하세요. 

`lookup`연산자는 다음과 같은 차이점이 있는 [join 연산자](joinoperator.md) 와 유사한 작업을 수행 합니다.

* 이렇게 하면 `$right` 조인 작업의 기준이 되는 테이블의 열이 반복 되지 않습니다.
* 두 종류의 조회가 지원 되며 `leftouter` `inner` `leftouter` 기본값은입니다.
* 성능 측면에서 시스템은 기본적으로 `$left` 테이블이 더 큰 (팩트) 테이블이 고 `$right` 테이블이 더 작은 (차원) 테이블 이라고 가정 합니다. 이는 연산자에서 사용 하는 가정과 정확히 반대입니다 `join` .
* 연산자는 테이블 `lookup` `$right` 을 테이블에 자동으로 브로드캐스트합니다 (기본적으로는 `$left` `hint.broadcast` 가 지정 된 것 처럼 동작). 이는 테이블의 크기를 제한 합니다 `$right` .

## <a name="syntax"></a>구문

*왼쪽 테이블* `|` `lookup`[ `kind` `=` ( `leftouter` | `inner` )] `(` *Righttable* `)` `on` *특성*

## <a name="arguments"></a>인수

* *왼쪽 테이블*: 조회의 기준이 되는 테이블 또는 테이블 형식 식입니다.
  로 표시 `$left` 됩니다.

* *Righttable*: 팩트 테이블에서 새 열을 "채우는" 데 사용 되는 테이블 또는 테이블 형식 식입니다. 로 표시 `$right` 됩니다.

* *특성*: *왼쪽 테이블* 의 행이 *righttable*의 행과 일치 하는 방식을 설명 하는 쉼표로 구분 된 하나 이상의 규칙 목록입니다. 논리 연산자를 사용 하 여 여러 규칙을 평가 `and` 합니다.
  규칙은 다음 중 하나일 수 있습니다.

  |규칙 종류        |구문                                          |Predicate                                                      |
  |-----------------|------------------------------------------------|---------------------------------------------------------------|
  |이름으로 같음 |*ColumnName*                                    |`where`*왼쪽 테이블*. *ColumnName* `==` *Righttable*. *ColumnName*|
  |값으로 같음|`$left.`*왼쪽 열* `==` `$right.` *Rightcolumn*|`where``$left.` *왼쪽* `==` 열 `$right.` * RightColumn        |

  > [!Note] 
  > ' 값이 같음 ' 인 경우 *열 이름은* 및 표기법으로 표시 된 해당 소유자 테이블로 한정 되어야 합니다 `$left` `$right` .

* `kind`: *Righttable*과 일치 하지 않는 *왼쪽 테이블* 의 행을 처리 하는 방법에 대 한 선택적 지침입니다. 기본적으로 `leftouter` 이 사용 됩니다. 즉, 해당 행이 연산자가 추가한 *righttable* 열의 누락 값에 대해 null 값을 사용 하 여 출력에 표시 됩니다. 을 `inner` 사용 하는 경우 이러한 행은 출력에서 생략 됩니다. (다른 종류의 조인은 p 연산자에서 지원 되지 않습니다 `looku` .)
  
## <a name="returns"></a>반환

다음을 포함하고 있는 테이블:

* 일치하는 키를 포함하여 두 테이블 각각의 모든 열에 대한 열.
  이름 충돌이 발생 하면 우변의 열 이름이 자동으로 바뀝니다.
* 입력된 테이블 간의 모든 일치 항목에 대한 행. 일치 항목은 한 테이블에서 선택된 행이며 모든 `on` 필드에 대해 다른 테이블의 행과 같은 값을 가지고 있습니다. 
* 특성 (조회 키)은 출력 테이블에 한 번만 표시 됩니다.

 * `kind` 지정 되지 않은 `kind=leftouter`

     내부 일치 외에도 일치 항목이 없더라도 왼쪽(및/또는 오른쪽)의 모든 행에 대한 행이 있습니다. 이 경우 일치하지 않는 출력 셀에는 null 값이 포함되어 있습니다.

 * `kind=inner`

     왼쪽 및 오른쪽에서 일치하는 행의 모든 조합에 대해 한 개의 출력 행이 있습니다.

## <a name="examples"></a>예

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
1       | Bill      | 게이트    | billg
2       | Bill      | Clinton  | billc
3       | Bill      | Clinton  | billc
4       | Steve     | Ballmer  | steveb
5       | Tim       | 요리     | 기타 c