---
title: 미리 보기 플러그인 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 미리 보기 플러그인에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 18bda0a4348d0c0eb2776bf124c57397f318a989
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81510985"
---
# <a name="preview-plugin"></a>미리 보기 플러그인

입력 레코드 집합에서 지정된 수의 행과 입력 레코드 집합의 총 레코드 수까지 테이블을 반환합니다.

```kusto
T | evaluate preview(50)
```

**구문**

`T``|` `evaluate` *NumberOfRows* 번호Ofrows `preview(``)`

**반환**

플러그인은 `preview` 두 개의 결과 테이블을 반환합니다.
* 지정된 수의 행을 가진 테이블입니다.
  예를 들어 위의 샘플 쿼리는 `T | take 50`실행과 같습니다.
* 입력 레코드 집합의 레코드 수를 보유하는 단일 행/열이 있는 테이블입니다.
  예를 들어 위의 샘플 쿼리는 `T | count`실행과 같습니다.

**팁**

복잡한 `evaluate` 필터가 포함된 테이블 형식 의 원본이나 대부분의 소스 테이블 열을 참조하는 필터가 앞에 [`materialize`](materializefunction.md) 오는 경우 이 함수를 사용하는 것이 좋습니다. 다음은 그 예입니다.

```kusto
let MaterializedT = materialize(T);
MaterializedT | evaluate preview(50)
```