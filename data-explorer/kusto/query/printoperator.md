---
title: 인쇄 연산자 - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 인쇄 연산자에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/16/2019
ms.openlocfilehash: b751b07446a74ef17002abac26e4c881a2da757b
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81510968"
---
# <a name="print-operator"></a>print 연산자

하나 이상의 스칼라 식을 가진 단일 행을 출력합니다.

```kusto
print x=1, s=strcat("Hello", ", ", "World!")
```

**구문**

`print`[*[열 이름]* `=` *스칼라 표현* [','...]

**인수**

* *ColumnName*: 출력의 단수 열에 할당할 옵션 이름입니다.
* *스칼라표현식*: 평가할 스칼라 표현식입니다.

**반환**

단일 셀에 평가된 *ScalarExpression의*값이 있는 단일 열, 단일 행 테이블입니다.

**예**

연산자는 `print` 하나 이상의 스칼라 식을 평가하고 결과 값에서 단일 행 테이블을 만드는 빠른 방법으로 유용합니다.
다음은 그 예입니다.

```kusto
print 0 + 1 + 2 + 3 + 4 + 5, x = "Wow!"
```

```kusto
print banner=strcat("Hello", ", ", "World!")
```